pipeline {

    agent {
        docker { image 'python:3.7.3-stretch' }
    }

    stages {

        def eks_url = ''
        
        stage('Checkout') {
            checkout scm
        }

        stage('Install dependencies') {
            steps {
                echo 'Installing build dependencies and configuring build environment'
                sh 'wget -O /bin/hadolint https://github.com/hadolint/hadolint/releases/download/v1.16.3/hadolint-Linux-x86_64 && chmod +x /bin/hadolint'

                // Install aws cli and use a default user created for jenkinns builds.
                sh 'pip install --upgrade pip'
                sh 'pip3 install awscli --upgrade --user'
                sh 'aws --version'
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

            }
        }

        stage('Image Scan') {

        }

        stage('Deploy to ECR') {
            when {

            }

            steps {
                // Install AWS CLI
                sh 'aws'
            }
        }

        stage('Deploy ECR Image to AWS EKS Cluster') {
            when {
                anyOf { branch 'release/*'; branch 'master'; branch 'develop'
            }
            steps {
                echo 'Deploying application to AWS EKS Cluster'
                // make sure to use a different namespace for a branch. Either release, master, develop
                sh 'aws'
            }
        }

        stage('Acceptance Tests') {

        }
    }

    post {
        always {
            archiveArtifacts artifacts: '', fingerprint: true
        }
    }
}