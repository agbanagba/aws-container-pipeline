pipeline {

    agent any

    stages {
        
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Linitng') {
            // Pylint, hadolint and cloudformation lint.
            steps {
                sh 'pylint --disable=R,C,W1203 ./app/app.py'
                sh 'hadolint Dockerfile'
            }
        }

        stage('Unit Tests') {

            steps {
                echo 'Unit Tests'
            }
        }

        stage('Image Build') {
            // Build image and save in workspace. Be sure to version properly before 

            steps {
                echo 'Image Build'
                // After building the image, make sure to tag it properly.
            }
        }

        stage('Image Scan') {
            steps {
                echo 'Scanning Docker Image with aquascan'

            }
        }

        stage('Deploy to ECR') {
            when {
                anyOf { branch 'release/*'; branch 'master'; branch 'develop' }
            }

            steps {
                // Install AWS CLI
                echo 'Deploying application image to AWS ECR.'
                sh 'aws'
            }
        }

        stage('Deploy ECR Image to AWS EKS Cluster') {
            when {
                anyOf { branch 'release/*'; branch 'develop' }
            }
            steps {
                echo 'Deploying application to AWS EKS Cluster'
                // make sure to use a different namespace for a branch. Either release, master, develop
                sh 'aws'
            }
        }

        stage('Deploy to production namespace in AWS EKS Cluster') {
            when {
                branch 'master'
            }
            steps {
                // Remember to ask for input before trying to deploy to production
                echo 'Deploying application container to production '
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '', fingerprint: true
        }
    }
}