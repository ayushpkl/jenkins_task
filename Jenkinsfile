pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ayushpkl/jenkins_task.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jenkins-task-app:latest .'
            }
        }
        stage('Run Container') {
            steps {
                sh '''
                    docker rm -f jenkins-task-app || true
                    docker run -d --name jenkins-task-app -p 3000:3000 jenkins-task-app:latest
                '''
            }
        }
    }
}
