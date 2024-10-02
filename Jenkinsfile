pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO_CAST = 'eltemume/cast'
        DOCKER_HUB_REPO_MOVIE = 'eltemume/movie'
        DOCKER_TAG = 'tag'
        DOCKER_HUB_CREDENTIALS_ID = 'f8290316-e09c-4759-8d72-820925f0b8a9'
        KUBE_NAMESPACE_DEV = 'dev'
        KUBE_NAMESPACE_QA = 'QA'
        KUBE_NAMESPACE_STAGING = 'staging'
        KUBE_NAMESPACE_PROD = 'prod'
        HELM_RELEASE_CAST = 'cast-service'
        HELM_RELEASE_MOVIE = 'movie-service'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    docker.build("${DOCKER_HUB_REPO_CAST}:${DOCKER_TAG}", "cast-service")
                    docker.build("${DOCKER_HUB_REPO_MOVIE}:${DOCKER_TAG}", "movie-service")
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_HUB_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_HUB_REPO_CAST}:${DOCKER_TAG}").push()
                        docker.image("${DOCKER_HUB_REPO_MOVIE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }
        stage('Deploy to Dev') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh """
                        helm upgrade --install ${HELM_RELEASE_CAST} ./cast-service-helm --namespace ${KUBE_NAMESPACE_DEV} \
                            --set image.repository=${DOCKER_HUB_REPO_CAST},image.tag=${DOCKER_TAG}
                        helm upgrade --install ${HELM_RELEASE_MOVIE} ./movie-service-helm --namespace ${KUBE_NAMESPACE_DEV} \
                            --set image.repository=${DOCKER_HUB_REPO_MOVIE},image.tag=${DOCKER_TAG}
                        """
                    }
                }
            }
        }
        stage('Deploy to QA') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh """
                        helm upgrade --install ${HELM_RELEASE_CAST} ./cast-service-helm --namespace ${KUBE_NAMESPACE_QA} \
                            --set image.repository=${DOCKER_HUB_REPO_CAST},image.tag=${DOCKER_TAG}
                        helm upgrade --install ${HELM_RELEASE_MOVIE} ./movie-service-helm --namespace ${KUBE_NAMESPACE_QA} \
                            --set image.repository=${DOCKER_HUB_REPO_MOVIE},image.tag=${DOCKER_TAG}
                        """
                    }
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh """
                        helm upgrade --install ${HELM_RELEASE_CAST} ./cast-service-helm --namespace ${KUBE_NAMESPACE_STAGING} \
                            --set image.repository=${DOCKER_HUB_REPO_CAST},image.tag=${DOCKER_TAG}
                        helm upgrade --install ${HELM_RELEASE_MOVIE} ./movie-service-helm --namespace ${KUBE_NAMESPACE_STAGING} \
                            --set image.repository=${DOCKER_HUB_REPO_MOVIE},image.tag=${DOCKER_TAG}
                        """
                    }
                }
            }
        }
        stage('Deploy to Prod') {
            when {
                branch 'master'
            }
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                        sh """
                        helm upgrade --install ${HELM_RELEASE_CAST} ./cast-service-helm --namespace ${KUBE_NAMESPACE_PROD} \
                            --set image.repository=${DOCKER_HUB_REPO_CAST},image.tag=${DOCKER_TAG}
                        helm upgrade --install ${HELM_RELEASE_MOVIE} ./movie-service-helm --namespace ${KUBE_NAMESPACE_PROD} \
                            --set image.repository=${DOCKER_HUB_REPO_MOVIE},image.tag=${DOCKER_TAG}
                        """
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment to Kubernetes succeeded!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}

