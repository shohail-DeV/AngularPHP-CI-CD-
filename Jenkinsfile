pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        DOCKER_BUILDKIT = '1'
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shohail-DeV/AngularPHP-CI-CD-.git'
            }
        }

        stage('Docker Sanity Check') {
            steps {
                bat 'docker --version'
                bat 'docker compose version'
            }
        }

        stage('Build Docker Images') {
            steps {
                bat 'docker compose build'
            }
        }

        stage('Deploy Containers') {
            steps {
                bat '''
                docker compose down
                docker compose up -d
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                bat 'docker ps'
            }
        }
    }

    post {
        success {
            echo '✅ Angular + PHP CI/CD pipeline completed successfully'
        }
        failure {
            echo '❌ Pipeline failed !!'
        }
    }
}
