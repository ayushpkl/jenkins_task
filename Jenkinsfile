pipeline {
    agent any

    environment {
        // Point npm cache to /tmp to avoid PRoot rename() issues in /home/userland
        NPM_CONFIG_CACHE = '/tmp/npm-cache'
        // Prevent Jenkins from killing your background Node.js server when the build finishes
        JENKINS_NODE_COOKIE = 'dontKillMe'
        PORT = '3000'
    }

    options {
        // Keep only the last 5 builds so UserLAnd storage doesn't fill up
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timeout(time: 15, unit: 'MINUTES')
    }

    stages {
        // Note: Jenkins automatically runs "Declarative: Checkout SCM" before the first stage,
        // so a separate manual Checkout stage isn't needed.

        stage('Install Dependencies') {
            steps {
                sh '''
                    rm -rf /tmp/npm-cache
                    npm install \
                        --cache /tmp/npm-cache \
                        --maxsockets 1 \
                        --no-audit \
                        --no-fund
                '''
            }
        }

        stage('Start Service') {
            steps {
                sh '''
                    # Stop any previously running instance of the app cleanly
                    pkill -f "node.*app.js" || true
                    sleep 1

                    # Start the Node.js service in the background detached from Jenkins stdout
                    nohup npm start > app.log 2>&1 < /dev/null &

                    # Give it a couple of seconds and verify the process stayed alive
                    sleep 3
                    pgrep -f "node" > /dev/null || { echo "Server failed to start:"; cat app.log; exit 1; }
                '''
            }
        }
    }

    post {
        always {
            // Clean up temp npm cache to save space on Android storage
            sh 'rm -rf /tmp/npm-cache || true'
        }
        success {
            echo "Deployment succeeded! App is running on port ${PORT}."
        }
        failure {
            echo "Pipeline failed. Check console output or app.log in workspace."
        }
    }
}
