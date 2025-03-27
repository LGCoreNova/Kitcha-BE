pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Eureka') {
            steps {
                dir('eureka') {
                    // gradlew에 실행 권한 부여 (추가!)
                    sh 'chmod +x ./gradlew'
                    
                    // 그 다음 빌드 진행
                    sh './gradlew clean build -x test --no-daemon'
                    
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-creds') {
                            docker.build('jaehongpark04/eureka-service', '.').push('latest')
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker-compose -f docker-compose.yaml up -d eureka'
            }
        }
    }
}
