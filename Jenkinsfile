pipeline {
    agent any

    environment {
        BRANCH_NAME = "${env.GIT_BRANCH}"
        APP_PORT = "${env.GIT_BRANCH == 'main' ? '3000' : '3001'}"
        IMAGE_NAME = "myapp:${env.GIT_BRANCH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: BRANCH_NAME, url: 'https://github.com/JuanEstebanOsma1012/cicd-pipeline-EPAM'
            }
        }

        stage('Build') {
            agent { 
                docker { 
                    image 'node:20' 
                    args '-v $PWD:/app -w /app' // Monta el workspace dentro del contenedor
                } 
            }
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            agent { 
                docker { 
                    image 'node:20' 
                    args '-v $PWD:/app -w /app' // Monta el workspace dentro del contenedor
                } 
            }
            steps {
                sh 'npm test'
            }
        }

        stage('Change Logo') {
            steps {
                sh """
                cp logos/${BRANCH_NAME}-logo.svg public/logo.svg
                """
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${IMAGE_NAME} .
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                docker run -d -p ${APP_PORT}:3000 --name myapp-${BRANCH_NAME} ${IMAGE_NAME}
                """
            }
        }
    }
}
