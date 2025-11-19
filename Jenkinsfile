pipeline {
    agent any

    tools {
        nodejs 'NodeJS_18'   // Jenkins → Global Tool Configuration → NodeJS
    }

    environment {
        DOCKERHUB = credentials('dockerhub-creds')
        IMAGE_NAME = "yourdockerhubusername/trading-ui"
        BRANCH_NAME = "master"   // change if using main
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH_NAME}", url: 'https://github.com/betawins/Trading-UI.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Project') {
            steps {
                sh 'export NODE_OPTIONS=--openssl-legacy-provider && CI=false npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                        docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                        docker push ${IMAGE_NAME}:latest
                        """
                    }
                }
            }
        }

        stage('Deploy to Server (Optional)') {
            when {
                expression { return false }  // Set to true when server is ready
            }
            steps {
                sh """
                ssh ubuntu@your-server-ip '
                    docker pull ${IMAGE_NAME}:latest &&
                    docker stop trading-ui || true &&
                    docker rm trading-ui || true &&
                    docker run -d --name trading-ui -p 80:80 ${IMAGE_NAME}:latest
                '
                """
            }
        }
    }

    post {
        success {
            echo "🎉 Build & Deployment Successful!"
        }
        failure {
            echo "❌ Build Failed. Check logs for errors."
        }
    }
}
