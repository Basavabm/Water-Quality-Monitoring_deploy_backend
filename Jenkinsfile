pipeline {
    agent any

    environment {
        IMAGE_NAME = "basavabm/wqm-backend"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Basavabm/Water-Quality-Monitoring_deploy_backend.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
            }
        }

        stage('Docker Hub Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${IMAGE_TAG}'
            }
        }
    }

    post {
        success {
            echo 'Build and Docker Push Completed Successfully!'
            sh 'docker logout'
        }

        failure {
            echo 'Pipeline Failed!'
        }

        always {
            cleanWs()
        }
    }
}
