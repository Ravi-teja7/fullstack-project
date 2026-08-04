pipeline {
agent any

```
environment {
    NEXUS_URL = 'http://16.170.246.118:8081'
    REPOSITORY = 'raw-artifacts'
}

stages {

    stage('Checkout') {
        steps {
            checkout scm
        }
    }

    stage('SonarQube Analysis') {
        steps {
            script {
                def scannerHome = tool 'SonarScanner'
                withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
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
                curl -u $NEXUS_USER:$NEXUS_PASS \
                  --upload-file frontend/frontend-dist.tar.gz \
                  $NEXUS_URL/repository/$REPOSITORY/frontend-dist.tar.gz
                '''
            }
        }
    }
}
```

}
