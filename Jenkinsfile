pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'lbadu/heart-disease-notebook:latest'
        KUBECONFIG = credentials('kubeconfig-file')  // Assumindo que você tem um kubeconfig armazenado como credencial no Jenkins
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
                    // Autenticar no Kubernetes
                    sh 'kubectl config set-cluster my-cluster --server=https://<K8s-api-server> --certificate-authority=<path-to-ca-cert>'
                    sh 'kubectl config set-credentials my-user --token=<your-access-token>'
                    sh 'kubectl config set-context my-context --cluster=my-cluster --namespace=default --user=my-user'
                    sh 'kubectl config use-context my-context'
                    
                    // Aplicar as configurações
                    sh "kubectl apply -f deploymentsvc.yaml"
                    sh "kubectl apply -f service.yaml"
                }
            }
        }
    }
}
