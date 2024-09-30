pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials-id')
    }
    stages {
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
}

def deployToK8s(namespace) {
    sh """
    kubectl apply -f k8s/movie-service-deployment.yaml --namespace=${namespace}
    kubectl apply -f k8s/movie-service-service.yaml --namespace=${namespace}
    kubectl apply -f k8s/cast-service-deployment.yaml --namespace=${namespace}
    kubectl apply -f k8s/cast-service-service.yaml --namespace=${namespace}
    """
}
