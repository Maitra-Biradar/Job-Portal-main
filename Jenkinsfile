pipeline {
    agent any

    tools {
        jdk 'jdk'
        maven 'maven'
    }

    environment {
        APP_NAME = "jobportal"
        DEPLOY_DIR = "/home/ec2-user/jobportal"
        SERVER_IP = "13.204.155.5"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Maitra-Biradar/job-Portal-main.git'
            }
        }

        stage('Build Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Copy Jar to Server') {
            steps {
                sh """
                scp target/*.jar ec2-user@${SERVER_IP}:${DEPLOY_DIR}/app.jar
                """
            }
        }

        stage('Stop Old App') {
            steps {
                sh """
                ssh ec2-user@${SERVER_IP} '
                pkill -f app.jar || true
                '
                """
            }
        }

        stage('Start New App') {
            steps {
                sh """
                ssh ec2-user@${SERVER_IP} '
                nohup java -jar ${DEPLOY_DIR}/app.jar > app.log 2>&1 &
                '
                """
            }
        }
    }

    post {
        success {
            echo "JobPortal deployed successfully"
        }
        failure {
            echo "Deployment failed"
        }
    }
}
