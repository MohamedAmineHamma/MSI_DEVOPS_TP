pipeline {
  agent any

  options {
    timestamps()
    skipDefaultCheckout(true)
  }

  environment {
    IMAGE_NAME = "tp-msi-jenkins:latest"
    CONTAINER_NAME = "tp-msi-jenkins-demo"
    TO_EMAIL = "medamine.hamma.9@gmail.com"
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
        bat "docker build -t %IMAGE_NAME% ."
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

    success {
      echo "Sending SUCCESS mail to ${env.TO_EMAIL}"
      mail to: "${env.TO_EMAIL}",
           from: "medamine.hamma.9@gmail.com",
           subject: "SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
           body: """La pipeline Jenkins s’est terminée avec succès.

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
Result: SUCCESS

URL: ${env.BUILD_URL}
"""
    }

    failure {
      echo "Sending FAILURE mail to ${env.TO_EMAIL}"
      mail to: "${env.TO_EMAIL}",
           from: "medamine.hamma.9@gmail.com",
           subject: "FAILURE: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
           body: """La pipeline Jenkins a échoué.

Job: ${env.JOB_NAME}
Build: ${env.BUILD_NUMBER}
Result: FAILURE

URL: ${env.BUILD_URL}
"""
    }

    always {
      echo "pipeline finished: ${currentBuild.currentResult}"
    }

  }
}
