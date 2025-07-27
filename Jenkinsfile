@Library('jenkins-shared-libraries') _
pipeline {
    agent any

    environment{
        DOCKER_FRONT_END_IMAGE_NAME = "ritikumar38921/chatapp-frontend"
        DOCKER_BACKEND_END_IMAGE_NAME = "ritikumar38921/chatapp-backend"
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
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
                                dockerfile: './frontend/Dockerfile',
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
                                dockerfile: './backend/Dockerfile',
                                context: "./backend"
                            )
                        }
                    }
                }
            }  
        }

        stage('Push Docker Images') {
            parallel{
                stage("Pushing frontend Docker image to docker hub"){
                    steps{
                        script {
                            docker_push(
                                imageName: env.DOCKER_FRONT_END_IMAGE_NAME,
                                imageTag: env.imageTag,
                                credentials: "docker-hub-credentials",
                            )
                        }
                    }
                }
                stage("Pushing backend Docker image to docker hub"){
                    steps{
                        script {
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

        stage('Deploy to Kubernetes') {
            steps {
               withEnv(["KUBECONFIG=C:\\Users\\RITIK\\.kube\\config"]) {
                script {
                        sh 'kubectl config use-context minikube'

                        bat """
                            helm upgrade --install chatapp ./chat-app ^
                                --set backend.image=${DOCKER_BACKEND_END_IMAGE_NAME}:latest ^
                                --set frontend.image=${DOCKER_FRONT_END_IMAGE_NAME}:latest
                        """
                    }
                }
            }
        }

        stage('Port Forwarding'){
            steps{
                script{
                    sh """
                        BACKEND_POD=\$(kubectl get pods -n chat-app -l app=backend -o jsonpath="{.items[0].metadata.name}")
                        FRONTEND_POD=\$(kubectl get pods -n chat-app -l app=frontend -o jsonpath="{.items[0].metadata.name}")

                        echo "Backend Pod: \$BACKEND_POD"
                        echo "Frontend Pod: \$FRONTEND_POD"

                        kubectl port-forward pod/\$BACKEND_POD -n chat-app 5001:5001 &
                        kubectl port-forward pod/\$FRONTEND_POD -n chat-app 6000:80 &
                    """
                }
            }
        }
    }
}