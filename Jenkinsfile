pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('f8290316-e09c-4759-8d72-820925f0b8a9')
    }
    stages {
        stage('Cleanup') {
            steps {
                script {
                    docker.image('eltemume/movie:tag').remove(force: true, prune: true)
                    docker.image('eltemume/cast:tag').remove(force: true, prune: true)
                }
            }
        }
        stage('Build Movie Service') {
            steps {
                script {
                    movieServiceImage = docker.build("eltemume/movie:tag", "./movie-service")
                }
            }
        }
        stage('Build Cast Service') {
            steps {
                script {
                    castServiceImage = docker.build("eltemume/cast:tag", "./cast-service")
                }
            }
        }
        stage('Push Movie Service') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKERHUB_CREDENTIALS') {
                        movieServiceImage.push("tag")
                    }
                }
            }
        }
        stage('Push Cast Service') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKERHUB_CREDENTIALS') {
                        castServiceImage.push("tag")
                    }
                }
            }
        }
        stage('Deploy to Dev') {
            when {
                branch 'dev'
            }
            steps {
                deployToK8s('dev')
            }
        }
        stage('Deploy to QA') {
            when {
                branch 'qa'
            }
            steps {
                deployToK8s('qa')
            }
        }
        stage('Deploy to Staging') {
            when {
                branch 'staging'
            }
            steps {
                deployToK8s('staging')
            }
        }
        stage('Deploy to Prod') {
            when {
                branch 'master'
            }
            steps {
                deployToK8s('prod')
            }
        }
    }
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

def deployToK8s(namespace) {
    sh """
    kubectl apply -f k8s/movie-service-deployment.yaml --namespace=${namespace}
    kubectl apply -f k8s/movie-service-service.yaml --namespace=${namespace}
    kubectl apply -f k8s/cast-service-deployment.yaml --namespace=${namespace}
    kubectl apply -f k8s/cast-service-service.yaml --namespace=${namespace}
    """
}
