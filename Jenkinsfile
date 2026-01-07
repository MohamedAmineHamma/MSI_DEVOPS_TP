pipeline {
  agent any

  options {
    timestamps()
    skipDefaultCheckout(true)
  }

  environment {
    IMAGE_NAME = "tp-msi-jenkins:latest"
    CONTAINER_NAME = "tp-msi-jenkins-demo"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install') {
      steps {
        bat '''
          IF EXIST package-lock.json (
            npm ci
          ) ELSE (
            npm install
          )
        '''
      }
    }

    stage('Test') {
      steps {
        bat 'npm test'
      }
    }

    stage('Build Docker Image') {
      steps {
        bat 'docker build -t %IMAGE_NAME% .'
      }
    }

    stage('Run Container (Demo)') {
      steps {
        bat """
          docker rm -f %CONTAINER_NAME% 2>NUL
          docker run -d --name %CONTAINER_NAME% -p 3000:3000 %IMAGE_NAME%
          docker ps --filter "name=%CONTAINER_NAME%"
        """
      }
    }
  }

  post {
    always {
      echo "pipeline finished: ${currentBuild.currentResult}"
    }
  }
}
