pipeline {
    agent any

    stages {
<<<<<<< HEAD
        stage('Clone Repository') {
            steps {
                // Clone the repository from the master branch
                git url: 'https://github.com/Elmo36/Jenkins_devops_exams.git', branch: 'master'
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    // Pull existing images if needed
                    sh 'docker pull eltemume/cast:tag'
                    sh 'docker pull eltemume/movie:tag'
                }
            }
        }
        stage('Deploy to Kubernetes with Helm') {
            steps {
                script {
                    // Ensure Helm is initialized (optional based on your setup)
                    sh 'helm repo update'
                    
                    // Deploy using Helm to the dev namespace
                    sh 'helm upgrade --install jenkex ./jenkex --namespace dev'
=======
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
>>>>>>> bcc20e945f8295b8a4860bf0fb8306da75f76802
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
<<<<<<< HEAD
            echo 'Deployment successful!'
=======
            echo 'Deployment completed successfully!'
>>>>>>> bcc20e945f8295b8a4860bf0fb8306da75f76802
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
