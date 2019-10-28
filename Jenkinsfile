pipeline {
    agent any

    environment {
        AWS_ECR_URI = "642881291524.dkr.ecr.us-east-1.amazonaws.com/capstone"
        CAPSTONE_ML_APP = "capstone-ml-app"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing application dependencies'
                withPythonEnv('python3') {
                    sh 'pip3 install -r ./app/requirements.txt'
                }
            }
        }
        
        stage('Linitng') {
            steps {
                echo 'Performing liniting checks.'
                withPythonEnv('python3') {
                    sh 'pylint ./app/app.py'
                }
                sh 'hadolint Dockerfile'
            }
        }

        stage('Image Build') {
            steps {
                echo 'Image Build'
                sh "docker build --tag ${CAPSTONE_ML_APP}:latest ."
                sh 'docker images'
            }
        }

        stage('Deploy to ECR') {
            when {
                anyOf { branch 'master'; branch 'develop' }
            }
            steps {
                echo 'Deploying application image to AWS ECR.'
                sh '$(aws ecr get-login --region us-east-1 --no-include-email)'
                sh "docker tag ${CAPSTONE_ML_APP} ${AWS_ECR_URI}"
                sh "docker push ${AWS_ECR_URI}"
            }
        }

        stage('Deploy ECR Image to AWS EKS Cluster') {
            when {
                anyOf { branch 'master'; branch 'develop' }
            }
            steps {
                echo 'Deploying application to AWS EKS Cluster'
                sh 'kubectl apply -f k8s-templates/deployment.yml'
                sh 'kubectl apply -f k8s-templates/service.yml'
            }
        }
    }
}