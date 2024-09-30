pipeline {
    agent any
    stages {
        stage('Build Images') {
            parallel {
                stage('cast-service') {
                    steps {
                        script {
                            // Build the first Docker image
                            dir('cast-service') {
                                sh 'docker build -t eltemume/cast:latest .'
                            }
                        }
                    }
                }
                stage('movie-service') {
                    steps {
                        script {
                            // Build the second Docker image
                            dir('movie-service') {
                                sh 'docker build -t eltemume/movie:latest .'
                            }
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
                            // Run tests for the first app
                            sh 'docker run eltemume/cast:latest test'
                        }
                    }
                }
                stage('Test movie-service') {
                    steps {
                        script {
                            // Run tests for the second app
                            sh 'docker run eltemume/movie:latest test'
                        }
                    }
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                script {
                    // Deploy both applications to the staging environment
                    sh 'kubectl apply -f k8s/deployment-cast-staging.yaml -n staging'
                    sh 'kubectl apply -f k8s/deployment-movie-staging.yaml -n staging'
                }
            }
        }
        stage('Deploy to Production') {
            when {
                branch 'master'
            }
            steps {
                script {
                    // Deploy both applications to the production environment
                    sh 'kubectl apply -f k8s/deployment-cast-prod.yaml -n prod'
                    sh 'kubectl apply -f k8s/deployment-movie-prod.yaml -n prod'
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment completed successfully for both applications!'
        }
        failure {
            echo 'Deployment failed for one or both applications!'
        }
    }
}

