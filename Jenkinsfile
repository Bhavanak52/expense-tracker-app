pipeline {
    agent any

    environment {
        // Make sure these values are correct for your setup
        DOCKERHUB_USERNAME = 'bhavanak52' 
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
                    // Use 'bat' for Windows
                    bat 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    // Use 'bat' for Windows
                    bat 'npm install'
                    bat 'npm run build'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                // Use 'bat' for Windows
                bat "docker build -t ${env.DOCKERHUB_USERNAME}/expense-tracker-backend:latest ./backend"
                bat "docker build -t ${env.DOCKERHUB_USERNAME}/expense-tracker-frontend:latest ./frontend"
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Use 'bat' for Windows. Note the double quotes for variable expansion.
                    bat "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                    bat "docker push ${env.DOCKERHUB_USERNAME}/expense-tracker-backend:latest"
                    bat "docker push ${env.DOCKERHUB_USERNAME}/expense-tracker-frontend:latest"
                }
            }
        }

        stage('Deploy Application') {
            steps {
                // Use 'bat' for Windows
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
