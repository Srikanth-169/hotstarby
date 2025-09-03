pipeline {
    agent any

    environment {
        DOCKERHUB_REPO = "srikanth"
        IMAGE_TAG = "v1"
        DOCKERHUB_USER = "srikanth-169"
    }

    stages {
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker rmi -f hotstar:v1 || true
                  docker build -t hotstar:v1 -f Dockerfile .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                  docker rm -f hotstar || true
                  docker run -d --name hotstar -p 8082:8080 hotstar:v1
                '''
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'decker_cred', // Jenkins credentials ID
                    usernameVariable: 'srikanth-169'
                    passwordVariable: 'Sk@628187'
                sh '''
                  # Tag image with DockerHub repo
                  docker tag hotstar:v1 ${DOCKERHUB_REPO}:${IMAGE_TAG}

                  # Login to DockerHub using credentials
                  echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin

                  # Push image
                  docker push ${DOCKERHUB_REPO}:${IMAGE_TAG}
                '''
            }
        }

        stage('Check Docker') {
            steps {
                sh 'docker ps'
            }
        }
    }
}
