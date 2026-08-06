pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "raviteja0090/fullstack-app:latest"
    }

    stages {

        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                sh "docker build -t ${DOCKER_IMAGE} frontend"
            }
        }

        stage("Push Docker Image") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push ${DOCKER_IMAGE}
                    '''
                }
            }
        }

        stage("Update Kubernetes Manifest") {
            steps {
                dir('/var/lib/jenkins/k8s-manifests') {
                    sh '''
                    sed -i 's|image: .*|image: raviteja0090/fullstack-app:latest|' deployment.yaml
                    '''
                }
            }
        }

        stage("Push Manifest Changes") {
            steps {
                dir('/var/lib/jenkins/k8s-manifests') {
                    withCredentials([usernamePassword(
                        credentialsId: 'github-creds',
                        usernameVariable: 'GIT_USER',
                        passwordVariable: 'GIT_PASS'
                    )]) {
                        sh '''
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins"

                        git add deployment.yaml
                        git commit -m "Update image version from Jenkins" || true

                        git remote set-url origin https://$GIT_USER:$GIT_PASS@github.com/Ravi-teja7/k8s-manifests.git

                        git push origin main
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build, SonarQube analysis, Docker build, Docker push, Git update, and Argo CD deployment completed successfully."
        }
    }
}
