pipeline {
    agent any 
    
    options{
     timestamps()
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

        stage('installation dependencies') {
            steps {
                echo 'installation dependencies'
                bat 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing...'
                bat 'npm test'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo 'Livraison'
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }
        
        stage('Run Container (Demo)') {
  steps {
    bat """
      REM Stop & remove container if it exists (ignore errors)
      docker rm -f %CONTAINER_NAME% 2>NUL

      REM Run new container
      docker run -d --name %CONTAINER_NAME% -p 3000:3000 %IMAGE_NAME%
    """
  }
}

    }
    
    post {
        always{
            echo "pipeline finished: ${currentBuild.currentResult}"
            
        }
    }    
}