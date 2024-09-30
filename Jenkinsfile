pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('your-dockerhub-credentials-id')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Movie Service') {
            steps {
                script {
                    // Build the Docker image for Movie Service
                    def movieImage = docker.build("movie-service:${env.BUILD_ID}")
                }
            }
        }

        stage('Build Cast Service') {
            steps {
                script {
                    // Build the Docker image for Cast Service
                    def castImage = docker.build("cast-service:${env.BUILD_ID}")
                }
            }
        }

        stage('Cleanup') {
            steps {
                script {
                    // Cleanup old images (if necessary)
                    docker.image("movie-service:${env.BUILD_ID}").remove() // Correct usage
                    docker.image("cast-service:${env.BUILD_ID}").remove()  // Correct usage
                }
            }
        }

        stage('Push Movie Service') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        docker.image("movie-service:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        stage('Push Cast Service') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        docker.image("cast-service:${env.BUILD_ID}").push()
                    }
                }
            }
        }

        stage('Deploy to Dev') {
            steps {
                script {
                    // Your deployment logic here
                }
            }
        }

        stage('Deploy to QA') {
            steps {
                script {
                    // Your deployment logic here
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    // Your deployment logic here
                }
            }
        }

        stage('Deploy to Prod') {
            steps {
                script {
                    // Your deployment logic here
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Cleanup can also be added here if needed
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
