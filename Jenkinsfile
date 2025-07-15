pipeline {
    agent any

    environment {
        COMPOSE_FILE = 'docker-compose.yml'   // Docker Compose file in the repo
        PROJECT_NAME = 'simple-html-web'      // Docker Compose project/container name
    }

    triggers {
        pollSCM('H/2 * * * *') // Check for changes every 2 minutes
    }

    stages {

        stage('Checkout Repository') {
            steps {
                echo 'Cloning latest code from GitHub...'
                checkout scm
            }
        }

        stage('Stop and Clean Existing Containers') {
            steps {
                script {
                    echo 'Checking for running containers...'
                    def containers = sh(script: "docker ps -q --filter name=${PROJECT_NAME}", returnStdout: true).trim()
                    
                    if (containers) {
                        echo 'Stopping and removing old containers...'
                        sh 'docker compose down --remove-orphans'
                    } else {
                        echo 'No existing containers to stop.'
                    }
                }
            }
        }

        stage('Build and Start with Docker Compose') {
            steps {
                echo 'Building and launching containers...'
                sh 'docker compose up --build -d'
            }
        }

        stage('Clean Up Dangling Images') {
            steps {
                echo 'Removing unused <none> Docker images...'
                sh '''
                    docker images -f "dangling=true" -q | xargs -r docker rmi -f || true
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully!'
        }
        failure {
            echo '❌ Deployment failed. Check console output for details.'
        }
        always {
            echo '📦 Jenkins pipeline execution complete.'
        }
    }
}
