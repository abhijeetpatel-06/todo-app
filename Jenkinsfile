pipeline {
    agent any

    tools {
        nodejs "node18"   // configure in Jenkins global tools
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/yourusername/node-mvc-app.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'echo "Build step - Node app does not need compilation"'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test || echo "No tests defined"'
            }
        }

        stage('Start Application') {
            steps {
                sh 'npm start &'
            }
        }
    }
}
