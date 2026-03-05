pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-1"
        ACCOUNT_ID = "245324547477"
        IMAGE_NAME = "245324547477.dkr.ecr.us-east-1.amazonaws.com/living_parallax"
        REGISTRY = "https://245324547477.dkr.ecr.us-east-1.amazonaws.com"
        CLUSTER_NAME = "web-living_parallax"
        SERVICE_NAME = "web-living_parallax-task-service-x8xr24es"
    }

    stages {

        stage('Fetch Code') {
            steps {
                git branch: 'main', url: 'https://github.com/SANJAI-S1/living_parallax.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${BUILD_NUMBER}", ".")
                }
            }
        }

        stage('Login & Push to ECR') {
            steps {
                script {
                    docker.withRegistry("${REGISTRY}", "ecr:us-east-1:keycrd") {
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push("latest")
                    }
                }
            }
        }

        stage('Deploy to ECS') {
            steps {
                withAWS(credentials: 'keycrd', region: 'us-east-1') {
                    sh """
                    aws ecs update-service \
                        --cluster ${CLUSTER_NAME} \
                        --service ${SERVICE_NAME} \
                        --force-new-deployment \
                        --region ${AWS_REGION}
                    """
                }
            }
        }
    }
}

