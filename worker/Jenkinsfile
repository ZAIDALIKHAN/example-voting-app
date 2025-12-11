pipeline {
    agent any

    environment {
        SERVICE       = "worker"
        AWS_ACCOUNT_ID = "085013191936"
        AWS_REGION     = "us-east-1"
        ECR_REPO       = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${SERVICE}"
        IMAGE          = "${ECR_REPO}:${BRANCH_NAME}"
    }

    triggers {
        githubPush()
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh """
                    echo "Building image for ${SERVICE}"
                    docker build -t ${IMAGE} .
                """
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                    aws ecr get-login-password --region ${AWS_REGION} \
                    | docker login --username AWS --password-stdin ${ECR_REPO}
                """
            }
        }

        stage('Push to ECR') {
            steps {
                sh "docker push ${IMAGE}"
            }
        }
    }

    post {
        always { echo "Pipeline completed for ${SERVICE}" }
    }
}
