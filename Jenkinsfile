pipeline {
agent any

```
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
}
```

}
