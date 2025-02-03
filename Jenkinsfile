pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'lbadu/heart-disease-notebook:latest'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/Badu1230/CI-CD-Jenkins-K8s'
            }
        }

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
                withCredentials([
                    file(credentialsId: 'ca-crt', variable: 'CA_CRT'),
                    file(credentialsId: 'client-crt', variable: 'CLIENT_CRT'),
                    file(credentialsId: 'client-key', variable: 'CLIENT_KEY')
                ]) {
                    script {
                        // Écrire le fichier kubeconfig dans le répertoire temporaire
                        sh '''
                        cat > kubeconfig << EOF
apiVersion: v1
clusters:
- cluster:
    certificate-authority: $CA_CRT
    server: https://127.0.0.1:32771
  name: minikube
contexts:
- context:
    cluster: minikube
    namespace: default
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: $CLIENT_CRT
    client-key: $CLIENT_KEY
EOF
                        '''
                        
                        sh 'kubectl apply -f deploymentsvc.yaml --kubeconfig=kubeconfig'
                        sh 'kubectl apply -f service.yaml --kubeconfig=kubeconfig'
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline terminée, avec ou sans succès!'
        }
        success {
            echo 'Pipeline réussie!'
        }
        failure {
            echo 'Pipeline échouée.'
        }
        cleanup {
            deleteDir() // Clean up the workspace
        }
    }
}
