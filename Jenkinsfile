pipeline {
    agent any
    tools {
        nodejs 'nodejs'  
    }
    environment {
        AWS_REGION = 'ap-northeast-2'
        ECR_REPO = '341162387145.dkr.ecr.ap-northeast-2.amazonaws.com/nsa'
        IMAGE_TAG = 'latest'
    }
    stages {
        /*stage('Checkout Github') {
            steps {
                #git branch: 'main', credentialsId: 'nsa0320', url: 'https://github.com/nsa0320/WebGoat.git'
            }
        } */
        stage('Install node dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test Code') {
            steps {
                sh 'npm test || echo "Tests failed, but continuing..."'
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
            echo ' Docker image built and pushed to ECR successfully!'
        }
        failure {
            echo ' Pipeline failed. Check logs.'
        }
    }
}
