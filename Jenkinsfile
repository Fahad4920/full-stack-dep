pipeline {
    agent any

    environment {
        COMPOSE_FILE = 'docker-compose.yml'  // Define the Docker Compose file name
        PROJECT_NAME = 'simple-html-web'  // The name of the project/container
    }

    triggers {
        pollSCM('H/1 * * * *')  // Poll GitHub every 5 minutes for changes
    }

    stages {
        stage('Check for Changes') {
            steps {
                script {
                    echo 'Checking for changes in the repository...'
                    checkout scm  // Pull the latest code from the repository
                }
            }
        }

        stage('Stop and Remove Existing Containers') {
            steps {
                script {
                    echo 'Checking for existing containers...'
                    // Check if the container related to this project exists
                    def containers = sh(script: "docker ps -q --filter name=${PROJECT_NAME}", returnStdout: true).trim()
                    if (containers) {
                        echo 'Stopping and removing existing containers...'
                        sh 'docker-compose down --remove-orphans'  // Use docker-compose
                    } else {
                        echo 'No existing containers found. Proceeding with the build.'
                    }
                }
            }
        }

        stage('Build and Start Containers') {
            steps {
                script {
                    echo 'Building and starting the application using Docker Compose...'
                    sh 'docker-compose up --build -d'  // Use docker-compose
                }
            }
        }

        stage('Remove Old <none> Images') {
            steps {
                script {
                    echo 'Removing old Docker images with <none> tag...'
                    sh 'docker images --filter "dangling=true" -q | xargs -r docker rmi -f'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully. The application is running!'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
        always {
            echo 'Pipeline run completed.'
        }
    }
}
