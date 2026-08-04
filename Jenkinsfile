pipeline {
agent any

environment {
    NEXUS_URL = 'http://YOUR_NEXUS_PUBLIC_IP:8081'
    REPOSITORY = 'raw-artifacts'
}

stages {

    stage('Checkout') {
        steps {
            checkout scm
        }
    }

    stage('Build Frontend') {
        steps {
            dir('frontend') {
                sh 'npm install'
                sh 'npm run build'
            }
        }
    }

    stage('Package Artifact') {
        steps {
            dir('frontend') {
                sh 'tar -czf frontend-dist.tar.gz dist'
            }
        }
    }

    stage('Upload Artifact to Nexus') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'nexus-creds',
                usernameVariable: 'NEXUS_USER',
                passwordVariable: 'NEXUS_PASS'
            )]) {
                sh '''
                curl -u ${NEXUS_USER}:${NEXUS_PASS} \
                --upload-file frontend/frontend-dist.tar.gz \
                ${NEXUS_URL}/repository/${REPOSITORY}/frontend-dist.tar.gz
                '''
            }
        }
    }
}

}
