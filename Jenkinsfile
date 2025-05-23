pipeline {
    agent {
        docker {
            image 'maven:3.9.3-eclipse-temurin-17'  // Użyj obrazu Mavena z JDK 17
            args '-v $HOME/.m2:/root/.m2' // Wymiana katalogu z repozytorium Maven na trwały
        }
    }

    environment {
        ACR_NAME = 'myacrjemek'
        ACR_LOGIN_SERVER = "${ACR_NAME}.azurecr.io"
        IMAGE_NAME = 'helloapp'
        TAG = "latest"
    }

    stages {

        stage('Install Docker') {
            steps {
                script {
                    // Instalacja Dockera wewnątrz kontenera (do operacji na Dockerze)
                    sh 'apt-get update && apt-get install -y docker.io'
                }
            }
        }

        stage('Build App') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${TAG} ."
            }
        }

        stage('Login to ACR') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'acr-credentials',
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
