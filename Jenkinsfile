pipeline {
    agent any

    environment {
        COMPOSE_FILE = 'docker-compose.yml'
        PROJECT_NAME = 'simple-html-web'
    }

    triggers {
        pollSCM('H/2 * * * *') // Poll every 2 minutes
    }

    stages {

        stage('Checkout Repository') {
            steps {
                echo '🔄 Cloning latest code from GitHub...'
                checkout scm
            }
        }

        stage('Check Environment') {
            steps {
                script {
                    echo '🔍 Checking Docker and Compose installation...'
                    // Check Docker installation
                    sh '''
                        echo "Docker Version:"
                        docker --version || { echo "❌ Docker is not installed or not in PATH."; exit 1; }

                        echo "Docker Compose Plugin Version:"
                        docker compose version || { echo "❌ Docker Compose plugin is not installed."; exit 1; }

                        echo "User and Groups Info:"
                        id

                        echo "Jenkins user should be in 'docker' group to run Docker commands."
                    '''
                }
            }
        }

        stage('Stop Existing Containers') {
            steps {
                script {
                    echo '🔎 Checking for running containers...'
                    def containers = sh(script: "docker ps -q --filter name=${PROJECT_NAME}", returnStdout: true).trim()
                    if (containers) {
                        echo "🛑 Stopping old containers..."
                        sh 'docker compose down --remove-orphans'
                    } else {
                        echo '✅ No containers to stop.'
                    }
                }
            }
        }

        stage('Build and Start Containers') {
            steps {
                echo '🚀 Building and starting containers...'
                script {
                    sh '''
                        set -x
                        docker compose up --build -d
                    '''
                }
            }
        }

        stage('Cleanup Dangling Images') {
            steps {
                echo '🧹 Cleaning up dangling Docker images...'
                sh '''
                    docker images -f "dangling=true" -q | xargs -r docker rmi -f || true
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment succeeded!'
        }
        failure {
            echo '❌ Deployment failed. Check console output above for errors.'
        }
        always {
            echo '📦 Pipeline finished.'
        }
    }
}
