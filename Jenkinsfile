pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t devops-app .'
            }
        }

        stage('Deploy to Dev') {
            steps {
                echo 'Deploying to Dev...'
                sh 'docker stop devops-app || true'
                sh 'docker rm devops-app || true'
                sh 'docker run -d -p 80:80 --name devops-app devops-app'
            }
        }

        stage('Approval') {
            steps {
                echo 'Waiting for manual approval...'
                input message: 'Deploy to Production?', ok: 'Yes, Deploy!'
            }
        }

        stage('Deploy to Prod') {
            steps {
                echo 'Deploying to Production...'
                sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@172.31.34.94 "
                        docker stop devops-app || true &&
                        docker rm devops-app || true &&
                        docker run -d -p 80:80 --name devops-app devops-app
                    "
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
