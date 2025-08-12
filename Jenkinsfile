pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'your-docker-username/devops-portfolio'
        KUBE_CONFIG = credentials('kubeconfig')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/victorblinks/devops-cloud-professional-profile.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Run Tests') {
            steps {
                sh 'echo "Running tests would go here"'
                // Example: sh 'npm test' if you had Node.js tests
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").push()
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh "kubectl apply -f kubernetes-deployment.yaml"
                    sh "kubectl rollout status deployment/devops-portfolio"
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            slackSend(color: 'good', message: "Build ${env.BUILD_ID} deployed successfully!")
        }
        failure {
            slackSend(color: 'danger', message: "Build ${env.BUILD_ID} failed!")
        }
    }
}