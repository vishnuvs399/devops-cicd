pipeline {
    agent any
 
    environment {
        DOCKER_IMAGE = "vishnu20005/myapp"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }
 
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vishnuvs399/devops-cicd.git'
            }
        }
 
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
            }
        }
 
        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
 
        stage('Deploy to Kubernetes') {
            steps {
                sh "kubectl apply -f deployment.yaml"
                sh "kubectl rollout status deployment/myapp-deployment"
                sh "kubectl get pods"
                sh "kubectl get svc"
            }
        }
    }
 
    post {
        success { echo 'Deployment Successful!' }
        failure { echo 'Pipeline Failed!' }
    }
}
 
