pipeline {
    agent any

    environment {
        IMAGE_NAME = 'egg-timer-app'
        CONTAINER_NAME = 'egg-timer-container'
    }

    stages {
        stage('Clone repo') {
            steps {
                git branch: 'main', url: 'https://github.com/pleszkowska/egg-timer-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Stop Previous Container') {
            steps {
                sh """
                    docker rm -f $CONTAINER_NAME || true
                """
            }
        }

        stage('Run Container') {
            steps {
                sh """
                    docker run -d -p 8080:80 --name $CONTAINER_NAME $IMAGE_NAME
                """
            }
        }
    }

    post {
        success {
            echo "Egg timer is running at http://localhost:8080/"
        }
    }
}
