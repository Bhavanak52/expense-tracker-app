pipeline {
    agent any

    environment {
        // NOTE: Replace these with your actual details
        DOCKERHUB_USERNAME = 'your-dockerhub-username'
        GITHUB_REPO = 'your-github-username/expense-tracker-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // This checks out the code from the repository that triggered the build
                checkout scm
            }
        }

        stage('Build Backend') {
            steps {
                // Navigate into the backend directory and install dependencies
                dir('backend') {
                    sh 'npm install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                // Navigate into the frontend directory, install dependencies, and build
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