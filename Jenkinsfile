pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-eks-app"
        AWS_REGION = "ap-south-1"
        ECR_REPO = "495737978491.dkr.ecr.ap-south-1.amazonaws.com"
    }

    stages {
        stage('Clone Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${ECR_REPO}:$BUILD_NUMBER")
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                        docker push ${ECR_REPO}:$BUILD_NUMBER
                    """
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    sh """
                        sed -i 's|<ECR_IMAGE_URL>|${ECR_REPO}:${BUILD_NUMBER}|' deployment/deployment.yaml
                        kubectl apply -f deployment/
                    """
                }
            }
        }
    }
}

