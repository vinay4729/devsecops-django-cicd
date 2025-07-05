pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'vinay4729/django-app'
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
        SONAR_TOKEN = credentials('sonar-token')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/vinay4729/devsecops-django-cicd.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_IMAGE
                    """
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh """ 
                        docker run --rm \\
                          -e SONAR_HOST_URL=http://35.176.84.101:9000 \\
                          -v \$(pwd):/usr/src \\
                          sonarsource/sonar-scanner-cli \\
                          -Dsonar.projectKey=django-app \\
                          -Dsonar.sources=/usr/src \\
                          -Dsonar.token=$SONAR_TOKEN
                    """
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
    }
}

