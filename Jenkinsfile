pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'heart-disease-notebook:latest'
        DOCKER_REGISTRY = 'docker.io'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Badu1230/Heart_Disease_(Matplotlib_Analysis).ipynb.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_REGISTRY}/${DOCKER_IMAGE}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-credentials') {
                        docker.image("${DOCKER_REGISTRY}/${DOCKER_IMAGE}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f deploymentsvc.yaml"
                    sh "kubectl apply -f service.yaml"

                }
            }
        }
    }
}

