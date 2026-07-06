pipeline {

    agent any

    environment {

        IMAGE_NAME = "node-demo"

        AWS_REGION = "ap-south-2"

        ACCOUNT_ID = "369602465346"

        ECR_REPO = "${ACCOUNT_ID}.dkr.ecr.ap-south-2.amazonaws.com/k8s-jenkin-repo"

    }

    stages {

        stage('Checkout') {

            steps {

                git branch: 'main',
                url: 'https://github.com/muhammedmusthafatp/k8s-jenkins-project.git'

            }

        }

        stage('Build Image') {

            steps {

                sh '''
                docker build -t $IMAGE_NAME ./app
                '''

            }

        }

        stage('Login ECR') {

            steps {

                sh '''
                aws ecr get-login-password --region $AWS_REGION \
                | docker login \
                --username AWS \
                --password-stdin $ACCOUNT_ID.dkr.ecr.ap-south-2.amazonaws.com
                '''

            }

        }

        stage('Push Image') {

            steps {

                sh '''

                docker tag $IMAGE_NAME:latest $ECR_REPO:latest

                docker push $ECR_REPO:latest

                '''

            }

        }

        stage('Deploy Kubernetes') {

            steps {

                sh '''

                kubectl apply -f k8s/

                kubectl rollout restart deployment/node-app -n jenkins-demo

                kubectl rollout status deployment/node-app -n jenkins-demo

                '''

            }

        }

    }

}
