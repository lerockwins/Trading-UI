pipeline {
    agent any
      

    stages {
        stage('Git checkout') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/betawins/Trading-UI.git'
                   }
}
        stage('Install npm prerequisites'){
            steps{

                sh'npm install'
                sh'CI=false npm run build'
                sh'pm2 delete Trading-UI || true'
                sh'pm2 --name Trading-UI start npm -- start'
            }
        }
    }
}
