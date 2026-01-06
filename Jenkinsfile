pipeline {
    agent {
        docker {
            image 'node:18'
      
        }
    }
    environment {
       GIT_CRED = 'git-cred-id'
    }


    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                url: 'https://github.com/your-repo.git/'
                credentialsId: "${GIT_CRED}", 
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
                echo 'Livraison de l\'application...'
                bat 'docker build -t tp-msio-jenkins .'
            }
        }
    }
}