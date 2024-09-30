pipeline {
    agent any

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Build Images') {
            parallel {
                stage('Build cast-service') {
                    steps {
                        script {
                            sh 'docker build -t eltemume/cast:tag ./cast-service'
                        }
                    }
                }
                stage('Build movie-service') {
                    steps {
                        script {
                            sh 'docker build -t eltemume/movie:tag ./movie-service'
                        }
                    }
                }
            }
        }

        stage('Test Images') {
            parallel {
                stage('Test cast-service') {
                    steps {
                        script {
                            sh 'docker run eltemume/cast:tag'
                        }
                    }
                }
                stage('Test movie-service') {
                    steps {
                        script {
                            sh 'docker run eltemume/movie:tag'
                        }
                    }
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    if (fileExists('k8s/deployment-cast-staging.yaml')) {
                        sh 'kubectl apply -f k8s/deployment-cast-staging.yaml -n staging'
                    } else {
                        error("Deployment file k8s/deployment-cast-staging.yaml does not exist.")
                    }
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                script {
                    // Check for production deployment file similarly
                    if (fileExists('k8s/deployment-cast-production.yaml')) {
                        sh 'kubectl apply -f k8s/deployment-cast-production.yaml -n production'
                    } else {
                        error("Deployment file k8s/deployment-cast-production.yaml does not exist.")
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            // Additional cleanup actions
        }
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed for one or both applications!'
        }
    }
}
