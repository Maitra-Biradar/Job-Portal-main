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
        JAR_NAME = "jobportal.jar"
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

        stage('Prepare Server') {
            steps {
                sh """
                ssh ec2-user@${SERVER_IP} '
                mkdir -p ${DEPLOY_DIR}
                '
                """
            }
        }

        stage('Copy Jar to Server') {
            steps {
                sh """
                scp target/*SNAPSHOT.jar ec2-user@${SERVER_IP}:${DEPLOY_DIR}/${JAR_NAME}
                """
            }
        }

        stage('Stop Old App') {
            steps {
                sh """
                ssh ec2-user@${SERVER_IP} '
                pkill -f ${JAR_NAME} || true
                '
                """
            }
        }

        stage('Start New App') {
            steps {
                sh """
                ssh ec2-user@${SERVER_IP} '
                nohup java -jar ${DEPLOY_DIR}/${JAR_NAME} \
                > ${DEPLOY_DIR}/app.log 2>&1 &
                sleep 5
                ps -ef | grep ${JAR_NAME} | grep -v grep
                '
                """
            }
        }
    }

    post {
        success {
            echo "✅ JobPortal deployed successfully"
        }
        failure {
            echo "❌ Deployment
