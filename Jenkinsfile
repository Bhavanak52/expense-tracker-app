pipeline {
    agent any

    environment {
        // --- UPDATE THESE VALUES ---
        DOCKERHUB_USERNAME = 'bhavanak52' // <-- Replace with your Docker Hub username
        GITHUB_REPO = 'Bhavanak52/expense-tracker-app' // <-- Replace with your GitHub repo
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
                    sh 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Build the backend Docker image
                    sh "docker build -t ${env.DOCKERHUB_USERNAME}/expense-tracker-backend:latest ./backend"
                    // Build the frontend Docker image
                    sh "docker build -t ${env.DOCKERHUB_USERNAME}/expense-tracker-frontend:latest ./frontend"
                }
            }
        }

        stage('Push Docker Images to Docker Hub') {
            steps {
                // Log in to Docker Hub using credentials stored in Jenkins
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                    // Push the backend image
                    sh "docker push ${env.DOCKERHUB_USERNAME}/expense-tracker-backend:latest"
                    // Push the frontend image
                    sh "docker push ${env.DOCKERHUB_USERNAME}/expense-tracker-frontend:latest"
                }
            }
        }

        stage('Deploy Application') {
            steps {
                // Use docker-compose to stop any running instances and start new ones
                sh 'docker-compose down'
                sh 'docker-compose up -d'
            }
        }
    }

    post {
        always {
            // Clean up the workspace after the build is complete
            cleanWs()
        }
    }
}
