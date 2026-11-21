pipeline {
    agent any

    environment {
        PATH = "/home/userland/.yarn/bin:/home/userland/.config/yarn/global/node_modules/.bin:${env.PATH}"
        JENKINS_NODE_COOKIE = 'dontKillMe'
        PORT = '3000'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        timeout(time: 15, unit: 'MINUTES')
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh '''
                    yarn --version
                    yarn install --network-concurrency 1 --non-interactive
                '''
            }
        }

        stage('Start Service') {
            steps {
                sh '''
                    # Stop any previously running instance of the app cleanly
                    pkill -f "node.*app.js" || true
                    sleep 1

                    # Start the Node.js service detached from Jenkins build streams
                    nohup yarn start > app.log 2>&1 < /dev/null &

                    # Verify the process stays running
                    sleep 3
                    pgrep -f "node" > /dev/null || { echo "Server failed to start:"; cat app.log; exit 1; }
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment succeeded! App is running on port ${PORT}."
        }
        failure {
            echo "Pipeline failed. Check console output or app.log in workspace."
        }
    }
}
