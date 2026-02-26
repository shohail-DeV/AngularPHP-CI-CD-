pipeline {
    agent any

    environment {
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
        DOCKER_BUILDKIT = '1'
    }

    options {
        skipDefaultCheckout(true)
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

        stage('K8s Debug') {
    steps {
        bat 'kubectl config view'
        bat 'kubectl config current-context'
        bat 'kubectl get nodes'
    }
}

        stage('Deploy to Kubernetes') {
    steps {
        bat '''
        kubectl apply -f k8s/
        kubectl rollout status deployment/angular
        kubectl rollout status deployment/php
        kubectl rollout status deployment/mysql
        '''
    }
}

        stage('Verify Deployment') {
    steps {
        bat '''
        kubectl get pods -o wide
        kubectl get svc
        '''
    }
}

        stage('Post-Deployment Monitoring Check') {
    steps {
        bat '''
        echo Checking Angular deployment...
        kubectl rollout status deployment/angular --timeout=60s || exit /b 1

        echo Checking PHP deployment...
        kubectl rollout status deployment/php --timeout=60s || exit /b 1

        echo Checking MySQL deployment...
        kubectl rollout status deployment/mysql --timeout=60s || exit /b 1

        echo Validating Prometheus...
        curl http://localhost:9090/-/healthy || exit /b 1

        echo Monitoring validation completed successfully
        '''
    }
}

        stage('Validate Prometheus Metrics') {
    steps {
        bat '''
        curl "http://localhost:9090/api/v1/query?query=up" > result.json
        type result.json
        '''
    }
}

     

    }

    post {
        success {
            echo ' Angular + PHP CI/CD pipeline execution completed successfully'
        }
        failure {
            echo ' Pipeline failed !!'
        }
    }
}
