pipeline {
    agent any

    triggers {
        // GitHub webhook will trigger instantly
        githubPush()
    }

    environment {
        APP_NAME = "vote"
        ECR_REGISTRY = "YOUR_AWS_ACCOUNT_ID.dkr.ecr.YOUR_REGION.amazonaws.com"
        IMAGE = "${ECR_REGISTRY}/${APP_NAME}:latest"
    }

    stages {

        stage('Changes Check') {
            when {
                changeset "vote/**"
            }
            steps {
                echo "Detected changes in vote/ — proceeding with pipeline."
            }
        }

        stage('Build Docker Image') {
            when {
                changeset "vote/**"
            }
            steps {
                script {
                    sh """
                        cd vote
                        docker build -t ${IMAGE} .
                    """
                }
            }
        }

        stage('Login to ECR & Push') {
            when {
                changeset "vote/**"
            }
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region YOUR_REGION \
                            | docker login --username AWS --password-stdin ${ECR_REGISTRY}

                        docker push ${IMAGE}
                    """
                }
            }
        }


    }

    post {
        always {
            echo "Pipeline execution completed."
        }
    }
}
