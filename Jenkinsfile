pipeline {
    agent any

    environment {
        ACR_NAME = 'myacrjemek'
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        IMAGE_NAME = 'helloapp'
        TAG = "latest"  // Możesz też ustawić na "${env.BUILD_NUMBER}" albo z GIT SHA
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/jemek27/HelloAzure.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${TAG} ."
                }
            }
        }

        stage('Login to ACR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'acr-credentials', // ustawinoe w Jenkinsie
                    usernameVariable: 'ACR_USERNAME',
                    passwordVariable: 'ACR_PASSWORD'
                )]) {
                    sh "docker login ${ACR_LOGIN_SERVER} -u $ACR_USERNAME -p $ACR_PASSWORD"
                }
            }
        }

        stage('Tag & Push Image') {
            steps {
                script {
                    def fullTag = "${ACR_LOGIN_SERVER}/${IMAGE_NAME}:${TAG}"
                    sh "docker tag ${IMAGE_NAME}:${TAG} ${fullTag}"
                    sh "docker push ${fullTag}"
                }
            }
        }
    }
}
