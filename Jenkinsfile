@Library('jenkins-shared-libraries') _
pipeline {
    agent any

    environment : {
        DOCKER_FRONT_END_IMAGE_NAME: "ritikumar38921/chatapp-frontend",
        DOCKER_BACKEND_END_IMAGE_NAME: "ritikumar38921/chatapp-backend",
        DOCKER_IMAGE_TAG : ${BUILD_NUMBER},
    }

    stages {
        stage('Workspace cleanup') {
            steps {
                script {
                    clean_workspace()
                }
            }
        }

        stage('Git: Code Checkout') {
            steps {
                script {
                    clone('https://github.com/Ritikkumar38921/chatApp.git','main')
                }
            }
        }

        stage('Building docker images') {
            parallel {
                stage("Build chatapp frontend docker image"){
                    steps {
                        script {
                            docker_build(
                                imageName: env.DOCKER_FRONT_END_IMAGE_NAME,
                                imageTag: env.DOCKER_IMAGE_TAG,
                                dockerfile: 'Dockerfile',
                                context: "./frontend"
                            )
                        }
                    }
                }
                stage("Build chatapp backend docker image"){
                    steps {
                        script {
                            docker_build(
                                imageName: env.DOCKER_BACKEND_END_IMAGE_NAME,
                                imageTag: env.DOCKER_IMAGE_TAG,
                                dockerfile: 'Dockerfile',
                                context: "./backend"
                            )
                        }
                    }
                }
            }  
        }

        stage('Security Scan with Trivy') {
            steps {
                script {
                    trivy_scan()     
                }
            }
        }

        stage('Push Docker Images') {
            parallel:{
                stage("Pushing frontend Docker image to docker hub"){
                    script : {
                        docker_push(
                            imageName: env.DOCKER_FRONT_END_IMAGE_NAME,
                            imageTag: env.imageTag,
                            credentials: "docker-hub-credentials",
                        )
                    }
                }
                stage("Pushing backend Docker image to docker hub"){
                    script : {
                        docker_push(
                            imageName: env.DOCKER_BACKEND_END_IMAGE_NAME,
                            imageTag: env.imageTag,
                            credentials: "docker-hub-credentials",
                        )
                    }
                }
            }
        }
    }
}