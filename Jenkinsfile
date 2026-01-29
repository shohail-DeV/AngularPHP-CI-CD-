pipeline {
    agent any

    environment {
        nodejs 'Node_js'
//        SONAR_PROJECT_KEY = 'angular-php-app'
        DOCKER_COMPOSE_FILE = 'docker-compose.yml'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shohail-DeV/AngularPHP-CI-CD-.git'
            }
        }

        stage('Angular Build & Test') {
            steps {
                dir('app') {
                    sh '''
                      npm install
                      npm run build --configuration production
                      npm test -- --watch=false --browsers=ChromeHeadless
                    '''
                }
            }
        }

        stage('PHP Syntax Check') {
            steps {
                dir('api/php') {
                    sh '''
                      php -l connect.php
                    '''
                }
            }
        }

        // stage('Static Code Analysis - SonarQube') {
        //     steps {
        //         withSonarQubeEnv('SonarQube-Server') {
        //             sh '''
        //               sonar-scanner \
        //               -Dsonar.projectKey=$SONAR_PROJECT_KEY \
        //               -Dsonar.sources=. \
        //               -Dsonar.language=js,php
        //             '''
        //         }
        //     }
        // }

        // stage('Quality Gate') {
        //     steps {
        //         timeout(time: 2, unit: 'MINUTES') {
        //             waitForQualityGate abortPipeline: true
        //         }
        //     }
        // }

        stage('Docker Build') {
            steps {
                sh '''
                  docker compose -f $DOCKER_COMPOSE_FILE build
                '''
            }
        }

        stage('Deploy Containers') {
            steps {
                sh '''
                  docker compose -f $DOCKER_COMPOSE_FILE down
                  docker compose -f $DOCKER_COMPOSE_FILE up -d
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful 🚀'
        }
        failure {
            echo 'Pipeline failed ❌'
        }
    }
}
