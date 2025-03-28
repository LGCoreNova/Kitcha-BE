pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                dir('eureka') {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                            docker.build('jaehongpark04/eureka-service', '.').push('latest')
                        }
                    }
                }
            }
        }

        stage('Deploy using Docker Compose') {
            steps {
                sh '''
                docker-compose pull eureka
                docker-compose up -d eureka
                '''
            }
        }
    }
}
