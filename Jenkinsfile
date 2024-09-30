pipeline {
    agent any // Use any available agent

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub
                git url: 'https://github.com/Elmo36/Jenkins_devops_exams.git', branch: 'master'
            }
        }

        stage('Build Cast Service') {
            steps {
                // Build the Docker image for the cast service
                script {
                    sh 'docker build -t eltemume/cast:tag ./cast-service'
                }
            }
        }

        stage('Build Movie Service') {
            steps {
                // Build the Docker image for the movie service
                script {
                    sh 'docker build -t eltemume/movie:tag ./movie-service'
                }
            }
        }

        stage('Test') {
            steps {
                // Add your testing commands here
                script {
                    sh 'echo "Running tests..."'
                    // For example: sh './run-tests.sh'
                }
            }
        }

        stage('Deploy to Dev') {
            steps {
                // Deploy to development environment
                script {
                    sh 'kubectl apply -f ./jenkex/templates/deployment.yaml -n dev'
                }
            }
        }

        stage('Deploy to QA') {
            steps {
                // Deploy to QA environment
                script {
                    sh 'kubectl apply -f ./jenkex/templates/deployment.yaml -n qa'
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                // Deploy to staging environment
                script {
                    sh 'kubectl apply -f ./jenkex/templates/deployment.yaml -n staging'
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                // Manual approval for production deployment
                input 'Deploy to Production?'
                script {
                    sh 'kubectl apply -f ./jenkex/templates/deployment.yaml -n prod'
                }
            }
        }
    }

    post {
        success {
            // Actions to perform on success
            echo 'Pipeline completed successfully!'
        }
        failure {
            // Actions to perform on failure
            echo 'Pipeline failed.'
        }
    }
}
