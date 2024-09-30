pipeline {
    agent any
    stages {
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
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

