pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = "raviteja0090/frontend-app:latest"
        BACKEND_IMAGE  = "raviteja0090/backend-app:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh '''
                docker build -t $FRONTEND_IMAGE ./frontend
                '''
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                docker build -t $BACKEND_IMAGE ./backend
                '''
            }
        }
                stage('Check Credentials') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
            echo "Username=$DOCKER_USER"
            echo "$DOCKER_PASS" | wc -c
            '''
        }
    }
}
      stage('Docker Login') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            sh '''
            export DOCKER_CLIENT_TIMEOUT=300
            export COMPOSE_HTTP_TIMEOUT=300

            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            '''
        }
    }
}

        stage('Push Images') {
            steps {
                sh '''
                docker push $FRONTEND_IMAGE
                docker push $BACKEND_IMAGE
                '''
            }
        }

        stage('Deploy to Kubernetes') {
    steps {
        sh '''
        kubectl cluster-info
        kubectl get nodes

        kubectl apply -f k8s/backend-deployment.yaml
        kubectl apply -f k8s/backend-service.yaml
        kubectl apply -f k8s/frontend-deployment.yaml
        kubectl apply -f k8s/frontend-service.yaml
        '''
    }
}

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get deployments
                kubectl get pods
                kubectl get services
                '''
            }
        }
    }
}
