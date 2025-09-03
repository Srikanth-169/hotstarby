pipeline {
    agent any
    environment {
        APP_NAME = "hotstar"
        IMAGE_NAME = "hotstar:latest"
        CONTAINER_NAME = "hotstar"
        HOST_PORT = "8082"
        CONTAINER_PORT = "8080"
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

       stage('Push to Docker Hub') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub_creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
            sh '''
                echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                docker tag hotstar:v1 $DOCKER_USER/hotstar:latest
                docker push $DOCKER_USER/hotstar:latest
            '''
        }
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

        stage('Check Docker') {
            steps {
                sh 'docker ps'
            }
        }
    }
}
