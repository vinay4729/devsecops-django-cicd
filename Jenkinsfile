pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        SONAR_TOKEN = credentials('sonar-token')
        SONAR_HOST_URL = "http://35.176.84.101:9000"
        IMAGE_NAME = "vinay4729/django-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/vinay4729/devsecops-django-cicd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Docker Image') {
            steps {
                sh """
                echo ${DOCKER_HUB_CREDENTIALS_PSW} | docker login -u ${DOCKER_HUB_CREDENTIALS_USR} --password-stdin
                docker push $IMAGE_NAME
                """
            }
        }

        stage('SonarQube Analysis') {
            steps {
                sh """
                docker run --rm \
                    -e SONAR_HOST_URL=$SONAR_HOST_URL \
                    -e SONAR_TOKEN=$SONAR_TOKEN \
                    -v $(pwd):/usr/src \
                    sonarsource/sonar-scanner-cli \
                    -Dsonar.projectKey=django-app \
                    -Dsonar.sources=/usr/src
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}
