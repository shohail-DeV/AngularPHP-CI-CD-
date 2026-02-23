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

        stage('Directories'){
            steps{
                bat 'dir'
            }
        }

        stage('Docker Sanity Check') {
            steps {
                bat 'docker --version'
                bat 'docker compose version'
            }
        }

        stage('Build & Push Images') {
    steps {
        script {
            docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {

                def angularImage = docker.build("shohail009/angular-app:latest", "./app")
                angularImage.push()

                def phpImage = docker.build("shohail009/php-backend:latest", "./api/php")
                phpImage.push()

                def mysqlImage = docker.build("shohail009/mysql-db:latest", "./api/db")
                mysqlImage.push()
            }
        }
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
