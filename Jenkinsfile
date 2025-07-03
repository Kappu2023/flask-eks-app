pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REPO_URI = '495737978491.dkr.ecr.ap-south-1.amazonaws.com/flask-eks-app'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${ECR_REPO_URI}:${IMAGE_TAG} ."
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS --password-stdin $ECR_REPO_URI
                    docker push ${ECR_REPO_URI}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh """
                    aws eks update-kubeconfig --region $AWS_REGION --name demo-cluster
                    kubectl set image deployment/my-app-deployment my-app-container=${ECR_REPO_URI}:${IMAGE_TAG} --namespace=default
                """
            }
        }
    }
}

