pipeline {
    agent any

    environment {
        NODE_OPTIONS = "--openssl-legacy-provider"
    }

    stages {
        stage('Git checkout') {
            steps {
                git 'https://github.com/betawins/Trading-UI.git'
            }
        }

        stage('Install npm prerequisites') {
            steps {
                sh 'npm install'
                sh 'CI=false npm run build'
                sh 'pm2 delete Trading-UI || true'
                sh 'pm2 --name Trading-UI start npm -- start'
            }
        }
    }
}
