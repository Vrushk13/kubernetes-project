pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vrush13/mywebsite"
        IMAGE_TAG = "v${v2}"
        KUBE_NAMESPACE = "dev"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${mywebsite}:${v2} ."
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${mywensite}:${v2}"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl set image deployment/mywebsite-deployment \
                    mywebsite=${mywebsite}:${v2} \
                    -n ${KUBE_NAMESPACE}
                """
            }
        }

        stage('Verify Rollout') {
            steps {
                sh "kubectl rollout status deployment/mywebsite-deployment -n ${KUBE_NAMESPACE}"
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful 🚀'
        }
        failure {
            echo 'Pipeline Failed ❌'
        }
    }
}
