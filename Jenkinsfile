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
                // İmajı build numarasıyla etiketliyoruz (Örn: ilk-uygulamam:5)
                sh "docker build -t ${DOCKER_HUB_USER}/ilk-uygulamam:${BUILD_NUMBER} ."
            }
        }

        stage('Login & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CRED_ID}", passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    sh "echo \$DOCKER_HUB_PASSWORD | docker login -u \$DOCKER_HUB_USERNAME --password-stdin"
                    // Build numaralı imajı buluta fırlatıyoruz
                    sh "docker push ${DOCKER_HUB_USER}/ilk-uygulamam:${BUILD_NUMBER}"
                }
            }
        }

        stage('K8s Deploy') {
            steps {
                // YAML dosyasındaki geçici yazıyı o anki dinamik build numarasıyla değiştiriyoruz
                sh "sed -i 's/BUILD_NUMBER_TAG/${BUILD_NUMBER}/g' deployment.yaml"
                
                // Kubernetes artık her seferinde yeni bir imaj ismi görecek ve podları güncelleyecek!
                sh "kubectl apply -f deployment.yaml"
            }
        }
    }
}
