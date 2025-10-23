pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-id')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/2300030370/helmfullstack.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    bat 'docker build -f backend/Dockerfile.backend -t hari305/ecommerce-backend2:v1 backend'
                    bat 'docker build -f frontend/Dockerfile.frontend -t hari305/ecommerce-frontend2:v1 frontend'
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                script {
                    bat 'docker login -u %DOCKERHUB_CREDENTIALS_USR% -p %DOCKERHUB_CREDENTIALS_PSW%'
                    bat 'docker push hari305/ecommerce-backend2:v1'
                    bat 'docker push hari305/ecommerce-frontend2:v1'
                }
            }
        }

        stage('Deploy Helm Chart') {
            steps {
                script {
                    bat '''
                    cd helm-chart
                    helm upgrade --install ecommerce-chart-deploy . --set backend.image=hari305/ecommerce-backend2:v1 --set frontend.image=hari305/ecommerce-frontend2:v1
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '''
                kubectl get all
                kubectl get ingress
                kubectl get hpa
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful ✅'
        }
        failure {
            echo 'Deployment Failed ❌'
        }
    }
}
