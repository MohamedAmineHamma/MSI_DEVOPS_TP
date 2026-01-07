pipeline {
  agent any

  options {
    timestamps()
    skipDefaultCheckout(true)
  }

  environment {
    IMAGE_NAME = "tp-msi-jenkins:latest"
    CONTAINER_NAME = "tp-msi-jenkins-demo"
    TO_EMAIL = "tonadresse@gmail.com"
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
    success {
      mail to: "${env.TO_EMAIL}",
           subject: "SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
           body: """Pipeline SUCCESS

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: SUCCESS

URL: ${env.BUILD_URL}
"""
    }

    failure {
      mail to: "${env.TO_EMAIL}",
           subject: "FAILURE: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
           body: """Pipeline FAILURE

Job: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Status: FAILURE

Consulte les logs ici:
${env.BUILD_URL}
"""
    }

    always {
      echo "pipeline finished: ${currentBuild.currentResult}"
    }
  }
}
