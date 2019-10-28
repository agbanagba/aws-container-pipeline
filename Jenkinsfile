pipeline {
    agent any
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
                sh "docker build --tag capstone-ml-app:latest ."
                sh 'docker images'
            }
        }

        stage('Deploy to ECR') {
            when {
                anyOf { branch 'master'; branch 'develop' }
            }
            steps {
                echo 'Deploying application image to AWS ECR.'
                // Check if there is a valid token to authenticate Docker to ECR registry
                sh 'aws'
            }
        }

        stage('Deploy ECR Image to AWS EKS Cluster') {
            when {
                anyOf { branch 'develop' }
            }
            steps {
                echo 'Deploying application to AWS EKS Cluster'
                sh 'kubectl apply --namespace development -f k8s-templates/deployment.yml'
                sh 'kubectl apply --namespace development -f k8s-templates/service.yml'
            }
        }

        stage('Deploy to production namespace in AWS EKS Cluster') {
            when {
                branch 'master'
            }
            steps {
                // Remember to ask for input before trying to deploy to production
                echo 'Deploying application container to production '
                sh 'kubectl apply --namespace production -f k8s-templates/deployment.yml'
                sh 'kubectl apply --namespace production -f k8s-templates/service.yml'
            }
        }
    }
}