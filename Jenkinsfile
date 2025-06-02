pipeline {
    agent any

    environment {
        IMAGE_NAME = 'egg-timer-app'
        CONTAINER_NAME = 'egg-timer-container'
    }

    stages {
        stage('Debug: Environment Info') {
            steps {
                echo "Printing environment variables:"
                bat 'set' // Windows equivalent of `env`
            }
        }

        stage('Clone repo') {
            steps {
                git branch: 'main', url: 'https://github.com/pleszkowska/egg-timer-app.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Debug: Docker Images') {
            steps {
                echo "Listing Docker images:"
                bat 'docker images'
            }
        }

        stage('Stop Previous Container') {
            steps {
                bat 'docker rm -f %CONTAINER_NAME% || exit 0'
            }
        }

        stage('Run Container') {
            steps {
                bat 'docker run -d -p 8081:80 --name %CONTAINER_NAME% %IMAGE_NAME%'
            }
        }

        stage('Debug: Running Containers') {
            steps {
                echo "Listing running Docker containers:"
                bat 'docker ps'
            }
        }
    }

    post {
        success {
            echo "Egg timer is running at http://localhost:8081/"
            emailext(
                subject: "SUCCESS: Egg Timer Pipeline",
                body: "The pipeline completed successfully.\nEgg timer is live at http://localhost:8081/",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: 'leszpaul3@gmail.com'
            )
        }

        failure {
            emailext(
                subject: "FAILURE: Egg Timer Pipeline",
                body: "The pipeline failed. Please check the Jenkins console output for details.",
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                to: 'leszpaul3@gmail.com'
            )
        }
    }
}   
