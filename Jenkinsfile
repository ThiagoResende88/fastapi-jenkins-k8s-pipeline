pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub'
        KUBECONFIG_CREDENTIALS_ID = 'minikube-kubeconfig'
        DOCKER_IMAGE_NAME = "thiagoresende/fastapi-jenkins-pipeline"
    }

    // 'stages' agrupa todas as etapas do nosso processo
    stages {
        // Etapa 1: Build da Imagem Docker
        stage('Build Docker Image') {
            steps {
                dir('backend') {
                    script {
                        sh "docker build -t ${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER} ."
                    }
                }
            }
        }

        // Etapa 2: Push para o Docker Hub
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "echo ${env.DOCKER_PASSWORD} | docker login -u ${env.DOCKER_USERNAME} --password-stdin"
                    sh "docker push ${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                    sh "docker tag ${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER} ${env.DOCKER_IMAGE_NAME}:latest"
                    sh "docker push ${env.DOCKER_IMAGE_NAME}:latest"
                }
            }
        }

        // ETAPA FINAL: Deploy no Kubernetes
        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: env.KUBECONFIG_CREDENTIALS_ID]) {
                    script {
                        sh "kubectl set image deployment/fastapi-deployment fastapi-container=${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"
                        sh "kubectl rollout status deployment/fastapi-deployment"
                    }
                }
            }
        }
    }

    post {
        success {
            chuckNorris()
        }
    }
}
