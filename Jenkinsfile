// Define a pipeline declarativa do Jenkins
pipeline {
    // Especifica que o pipeline pode rodar em qualquer agente (nó) disponível do Jenkins
    agent any

    // Define variáveis de ambiente que serão usadas durante a pipeline
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

        // ETAPA CORRIGIDA FINAL: Deploy no Kubernetes
        stage('Deploy to Kubernetes') {
            steps {
                // CORREÇÃO FINAL: Usando o parâmetro correto 'credentialsId'
                withKubeConfig([credentialsId: env.KUBECONFIG_CREDENTIALS_ID]) {
                    script {
                        // Comando para atualizar a imagem do contêiner no deployment existente.
                        sh "kubectl set image deployment/fastapi-deployment fastapi-container=${env.DOCKER_IMAGE_NAME}:${env.BUILD_NUMBER}"

                        // Comando para verificar o status do deploy e esperar até que ele seja concluído.
                        sh "kubectl rollout status deployment/fastapi-deployment"
                    }
                }
            }
        }
    }
}
