pipeline {
    agent any

    triggers {
        // Webhook-based triggering
        githubPush()
    }

    environment {
        AWS_ACCOUNT_ID  = "502487623078"
        AWS_REGION      = "us-east-1"
        ECR_REGISTRY    = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/demo"
        IMAGE           = "${ECR_REGISTRY}:latest"
    }

    stages {

        stage('Run Only If vote/ Changed') {
            when {
                changeset "vote/**"
            }
            steps {
                echo "Detected changes in vote/ folder — pipeline will continue."
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

        stage('Login to ECR') {
            when {
                changeset "vote/**"
            }
            steps {
                script {
                    sh """
                        aws ecr get-login-password --region ${AWS_REGION} \
                            | docker login --username AWS --password-stdin ${ECR_REGISTRY}
                    """
                }
            }
        }

        stage('Push Image to ECR') {
            when {
                changeset "vote/**"
            }
            steps {
                sh "docker push ${IMAGE}"
            }
        }

        // OPTIONAL — remove if you don’t want auto-deploy
        
    }

    post {
        always {
            echo "Pipeline completed."
        }
    }
}
