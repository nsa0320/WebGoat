pipeline {
    agent any
    tools {
        maven 'maven3.9'  
    }
    environment {
        AWS_REGION = 'ap-northeast-2'
        ECR_REPO = '341162387145.dkr.ecr.ap-northeast-2.amazonaws.com/nsa'
        IMAGE_TAG = 'latest'
    }
    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${ECR_REPO}:${IMAGE_TAG}")
                }
            }
        }
        stage('Push to AWS ECR') {
            steps {
                script {
                    sh '''
                    aws ecr get-login-password --region $AWS_REGION \
                    | docker login --username AWS --password-stdin $ECR_REPO

                    docker push $ECR_REPO:$IMAGE_TAG
                    '''
                }
            }
        }
    }
    post {
        success {
            echo '✅ Docker image built and pushed to ECR successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs.'
        }
    }
}


