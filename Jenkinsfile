pipeline {
    agent any

    tools {
        gradle "gradle8.12.1"
    }

    parameters {
        booleanParam(name: 'DOCKER_BUILD', defaultValue: true, description: 'Docker 이미지 빌드 실행 여부')
        string(name: 'DOCKER_IMAGE_TAG', defaultValue: '', description: 'Docker 이미지 태그 (비워두면 빌드 번호 사용)')
        booleanParam(name: 'TEST_MODE', defaultValue: true, description: '테스트 모드 (ECR 푸시하지 않음)')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Eureka Server') {
            steps {
                sh 'cd eureka && gradle clean build -x test'
            }
        }

        stage('Build Config Server') {
            steps {
                sh 'cd Config-server && gradle clean build -x test'
            }
        }

        stage('Build API Gateway') {
            steps {
                sh 'cd API-Gateway && gradle clean build -x test'
            }
        }

        stage('Docker Image Build - Eureka') {
            when {
                expression { params.DOCKER_BUILD == true }
            }
            steps {
                script {
                    def imageTag = params.DOCKER_IMAGE_TAG ?: "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
                    def ecrTagPrefix = "803691999553.dkr.ecr.us-west-1.amazonaws.com/kitcha/eureka"

                    def deployTag = "latest"
                    if (env.BRANCH_NAME != "main") {
                        deployTag = env.BRANCH_NAME
                    }

                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'toy-docker-server',
                            transfers: [sshTransfer(
                                cleanRemote: false,
                                excludes: '',
                                execCommand: """
                                    cd kitcha/eureka
                                    aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin 803691999553.dkr.ecr.us-west-1.amazonaws.com
                                    docker build --tag kitcha/eureka:${imageTag} -f Dockerfile .
                                    docker tag kitcha/eureka:${imageTag} ${ecrTagPrefix}:${imageTag}
                                    docker tag kitcha/eureka:${imageTag} ${ecrTagPrefix}:${deployTag}

                                    # 테스트 모드가 아닌 경우에만 푸시 실행
                                    if [ "${params.TEST_MODE}" = "false" ]; then
                                        echo "ECR에 이미지 푸시 중..."
                                        docker push ${ecrTagPrefix}:${imageTag}
                                        docker push ${ecrTagPrefix}:${deployTag}
                                    else
                                        echo "테스트 모드: ECR 푸시 건너뜀"
                                    fi
                                """,
                                execTimeout: 600000,
                                flatten: false,
                                makeEmptyDirs: false,
                                noDefaultExcludes: false,
                                patternSeparator: '[, ]+',
                                remoteDirectory: './kitcha/eureka',
                                remoteDirectorySDF: false,
                                removePrefix: 'eureka/build/libs',
                                sourceFiles: 'eureka/build/libs/*.jar'
                            )],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: true
                        )
                    ])
                }
            }
        }

        stage('Docker Image Build - Config Server') {
            when {
                expression { params.DOCKER_BUILD == true }
            }
            steps {
                script {
                    def imageTag = params.DOCKER_IMAGE_TAG ?: "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
                    def ecrTagPrefix = "803691999553.dkr.ecr.us-west-1.amazonaws.com/kitcha/config"

                    def deployTag = "latest"
                    if (env.BRANCH_NAME != "main") {
                        deployTag = env.BRANCH_NAME
                    }

                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'toy-docker-server',
                            transfers: [sshTransfer(
                                cleanRemote: false,
                                excludes: '',
                                execCommand: """
                                    cd kitcha/config
                                    aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin 803691999553.dkr.ecr.us-west-1.amazonaws.com
                                    docker build --tag kitcha/configserver:${imageTag} -f Dockerfile .
                                    docker tag kitcha/configserver:${imageTag} ${ecrTagPrefix}:${imageTag}
                                    docker tag kitcha/configserver:${imageTag} ${ecrTagPrefix}:${deployTag}

                                    # 테스트 모드가 아닌 경우에만 푸시 실행
                                    if [ "${params.TEST_MODE}" = "false" ]; then
                                        echo "ECR에 이미지 푸시 중..."
                                        docker push ${ecrTagPrefix}:${imageTag}
                                        docker push ${ecrTagPrefix}:${deployTag}
                                    else
                                        echo "테스트 모드: ECR 푸시 건너뜀"
                                    fi
                                """,
                                execTimeout: 600000,
                                flatten: false,
                                makeEmptyDirs: false,
                                noDefaultExcludes: false,
                                patternSeparator: '[, ]+',
                                remoteDirectory: './kitcha/config',
                                remoteDirectorySDF: false,
                                removePrefix: 'Config-server/build/libs',
                                sourceFiles: 'Config-server/build/libs/*.jar'
                            )],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: true
                        )
                    ])
                }
            }
        }

        stage('Docker Image Build - API Gateway') {
            when {
                expression { params.DOCKER_BUILD == true }
            }
            steps {
                script {
                    def imageTag = params.DOCKER_IMAGE_TAG ?: "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
                    def ecrTagPrefix = "803691999553.dkr.ecr.us-west-1.amazonaws.com/kitcha/apigateway"

                    def deployTag = "latest"
                    if (env.BRANCH_NAME != "main") {
                        deployTag = env.BRANCH_NAME
                    }

                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'toy-docker-server',
                            transfers: [sshTransfer(
                                cleanRemote: false,
                                excludes: '',
                                execCommand: """
                                    cd kitcha/apigateway
                                    aws ecr get-login-password --region us-west-1 | docker login --username AWS --password-stdin 803691999553.dkr.ecr.us-west-1.amazonaws.com
                                    docker build --tag kitcha/apigateway:${imageTag} -f Dockerfile .
                                    docker tag kitcha/apigateway:${imageTag} ${ecrTagPrefix}:${imageTag}
                                    docker tag kitcha/apigateway:${imageTag} ${ecrTagPrefix}:${deployTag}

                                    # 테스트 모드가 아닌 경우에만 푸시 실행
                                    if [ "${params.TEST_MODE}" = "false" ]; then
                                        echo "ECR에 이미지 푸시 중..."
                                        docker push ${ecrTagPrefix}:${imageTag}
                                        docker push ${ecrTagPrefix}:${deployTag}
                                    else
                                        echo "테스트 모드: ECR 푸시 건너뜀"
                                    fi
                                """,
                                execTimeout: 600000,
                                flatten: false,
                                makeEmptyDirs: false,
                                noDefaultExcludes: false,
                                patternSeparator: '[, ]+',
                                remoteDirectory: './kitcha/apigateway',
                                remoteDirectorySDF: false,
                                removePrefix: 'API-Gateway/build/libs',
                                sourceFiles: 'API-Gateway/build/libs/*.jar'
                            )],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: true
                        )
                    ])
                }
            }
        }

        stage('Deploy to Development') {
            when {
                expression { 
                    return env.BRANCH_NAME.startsWith('feat-') || env.BRANCH_NAME == 'develop' 
                }
            }
            steps {
                echo "개발 환경에 배포 중: ${env.BRANCH_NAME} 브랜치"
                // 개발 환경 배포 로직 추가
            }
        }

        stage('Deploy to Production') {
            when {
                expression { 
                    return env.BRANCH_NAME == 'main' 
                }
            }
            steps {
                echo "프로덕션 환경에 배포 중: main 브랜치"
                // 프로덕션 환경 배포 로직 추가
            }
        }
    }

    post {
        success {
            echo "빌드 및 배포 성공! 브랜치: ${env.BRANCH_NAME}"
        }
        failure {
            echo "빌드 또는 배포 실패! 브랜치: ${env.BRANCH_NAME}"
        }
        always {
            cleanWs()
        }
    }
}
