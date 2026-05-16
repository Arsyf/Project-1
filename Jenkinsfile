pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'devarsyf'
        DOCKER_CRED_ID  = 'docker-hub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Tag') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USER}/ilk-uygulamam:latest ."
            }
        }

        stage('Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CRED_ID}", passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    sh "echo \$DOCKER_HUB_PASSWORD | docker login -u \$DOCKER_HUB_USERNAME --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/ilk-uygulamam:latest"
                }
            }
        }

        stage('K8s Deploy') {
            steps {
                // Kubernetes'e YAML dosyasını uygulamasını söylüyoruz
                sh "kubectl apply -f deployment.yaml"
            }
        }
    }
}
