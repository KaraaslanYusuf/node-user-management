pipeline {
    agent any

    environment {
        // Define the Docker Hub credentials ID
        DOCKERHUB_CREDENTIALS = 'dockerh_pass'
        // Define the GitHub credentials ID
        GITHUB_CREDENTIALS = 'git_pass'
        // Define the GitHub repository URL
        GITHUB_REPO_URL = 'GITHUB REPO URL'
        // Define the Docker image name
        DOCKER_IMAGE_NAME = 'settrigh/devtest'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from GitHub using GitHub credentials
                script {
                    checkout([$class: 'GitSCM', branches: [[name: '*/master']], userRemoteConfigs: [[url: GITHUB_REPO_URL, credentialsId: GITHUB_CREDENTIALS]]])
                }
            }
        }

        stage('Build') {
            steps {
                // Build the Node.js application
                script {
                    sh 'npm install'
                }
            }
        }

        stage('Dockerize') {
            steps {
                // Build the Docker image
                script {
                    sh 'docker build -t $DOCKER_IMAGE_NAME .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                // Push the Docker image to Docker Hub using Docker Hub credentials
                script {
                    withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                        sh "docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD"
                        sh "docker push $DOCKER_IMAGE_NAME"
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up by removing the Docker image
            cleanWs()
            script {
                sh "docker rmi -f $DOCKER_IMAGE_NAME || true"
            }
        }
    }
}
