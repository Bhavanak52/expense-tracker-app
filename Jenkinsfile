pipeline {
    agent any

    environment {
        // --- THIS LINE WAS CHANGED ---
        DOCKERHUB_USERNAME = 'bhavanakajampady' // <-- Use the username you log in with
        GITHUB_REPO = 'Bhavanak52/expense-tracker-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    bat 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                bat "docker build -t ${env.DOCKERHUB_USERNAME}/expense-tracker-backend:latest ./backend"
                bat "docker build -t ${env.DOCKERHUB_USERNAME}/expense-tracker-frontend:latest ./frontend"
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                    bat "docker push ${env.DOCKERHUB_USERNAME}/expense-tracker-backend:latest"
                    bat "docker push ${env.DOCKERHUB_USERNAME}/expense-tracker-frontend:latest"
                }
            }
        }

        stage('Deploy Application') {
            steps {
                bat 'docker-compose down'
                bat 'docker-compose up -d'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
