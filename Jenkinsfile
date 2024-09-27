pipeline {
    agent any

    environment {
        REGISTRY = 'docker.io'
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'  // Jenkins credentials ID for DockerHub
        IMAGE_NAME = 'my-python-app'
        IMAGE_TAG = 'latest'
        // KUBECONFIG_PATH = '/home/jenkins/.kube/config'  // Path to kubeconfig file
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/Saikiran121/Jenkins-cicd-python.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://${REGISTRY}", "${DOCKERHUB_CREDENTIALS_ID}") {
                        docker.image("${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to MicroK8s') {
            steps {
                script {
                    sh """
                    # Apply Kubernetes configuration using kubectl in MicroK8s
                    microk8s kubectl apply -f k8s/deployment.yaml
                    microk8s kubectl apply -f k8s/service.yaml
                    """
                }
            }
        }
    }

    post {
        always {
            script {
                docker.image("${REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}").remove()
            }
            cleanWs()
        }
    }
}

