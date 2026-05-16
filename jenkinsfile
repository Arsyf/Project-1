pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = 'devarsyf'
    }
    stages {
        stage('Build & Tag') {
            steps {
                sh "docker build -t ${DOCKER_HUB_USER}/ilk-uygulamam:latest ."
            }
        }
        stage('Login & Push') {
            steps {
                // Jenkins'e tanıttığımız credential'ı güvenli şekilde çağırıyoruz
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    sh "echo \$DOCKER_HUB_PASSWORD | docker login -u \$DOCKER_HUB_USERNAME --password-stdin"
                    sh "docker push ${DOCKER_HUB_USER}/ilk-uygulamam:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                // 1. Varsa eski konteyneri durdurup siliyoruz
                sh "docker rm -f benim-app-konteyner || true"
                
                // 2. Önbellekteki eski imajı siliyoruz ki en yenisini çeksin (Opsiyonel ama garantidir)
                sh "docker rmi ${DOCKER_HUB_USER}/ilk-uygulamam:latest || true"
                
                // 3. Docker Hub'dan en güncel imajı çekiyoruz
                sh "docker pull ${DOCKER_HUB_USER}/ilk-uygulamam:latest"
                
                // 4. Yeni imajı 8081 portunda ayağa kaldırıyoruz
                sh "docker run -d --name benim-app-konteyner -p 8081:80 ${DOCKER_HUB_USER}/ilk-uygulamam:latest"
            }
        }
    }

    post {
        success {
            echo "Pipeline başarıyla tamamlandı ve uygulama güncellendi."
        }
        failure {
            echo "Bir şeyler ters gitti, logları kontrol etmelisin."
        }
    }
}
