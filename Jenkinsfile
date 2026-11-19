pipeline {
    agent any

    environment {
        PORT = '3000'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ayushpkl/jenkins_task.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Start Service') {
            steps {
                sh '''
                    pkill -f "node server.js" || true
                    nohup node server.js >/tmp/jenkins_task.log 2>&1 &
                    sleep 2
                    ps aux | grep "node server.js" | grep -v grep
                '''
            }
        }
    }
}
