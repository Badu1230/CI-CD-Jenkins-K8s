pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'lbadu/heart-disease-notebook:latest'
        KUBECONFIG = credentials('kubeconfig-file')  // Usando a credencial do kubeconfig
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Badu1230/CI-CD-Jenkins-K8s'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (isUnix()) {
                        sh 'docker --version'
                    } else {
                        bat 'docker --version'
                    }
                    def image = docker.build("${DOCKER_IMAGE}")
                    env.DOCKER_IMAGE = image.id
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        docker.image(env.DOCKER_IMAGE).push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Autenticar no Kubernetes usando o kubeconfig
                    sh 'kubectl apply -f deploymentsvc.yaml --kubeconfig=${KUBECONFIG}'
                    sh 'kubectl apply -f service.yaml --kubeconfig=${KUBECONFIG}'
                }
            }
        }
    }
}

