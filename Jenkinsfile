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

        stage('Verify Docker Environment') {
            steps {
                sh '''
                    echo "Docker Version:"
                    docker --version || { echo "❌ Docker not found."; exit 1; }

                    echo "Docker Compose Plugin Version:"
                    docker compose version || { echo "❌ Docker Compose plugin not installed."; exit 1; }

                    echo "Running as user: $(whoami)"
                    id
                '''
            }
        }

        stage('Stop Existing Containers') {
            steps {
                script {
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
                sh 'docker compose up --build -d'
            }
        }

        stage('Clean Up Dangling Images') {
            steps {
                echo '🧹 Cleaning up unused Docker images...'
                sh 'docker images -f "dangling=true" -q | xargs -r docker rmi -f || true'
            }
        }
    }

    post {
        success {
            echo '✅ Deployment completed successfully.'
        }
        failure {
            echo '❌ Deployment failed. See error logs above.'
        }
        always {
            echo '🧩 Pipeline run completed.'
        }
    }
}
