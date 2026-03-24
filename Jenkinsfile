pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/omargriffin14/flask-docker-app.git'
            }
        }

    stages {
        stage('Clone Repository') {
            steps {
                git credentialsId: 'github-credentials',
                    url: 'https://github.com/omargriffin14/flask-docker-app.git',
                    branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker-compose build'
            }
        }

        stage('Deploy Containers') {
            steps {
                bat 'docker-compose down'
                bat 'docker-compose up -d'
            }
        }
    }

    post {
        success {
            echo 'Deployment successful! App is running at http://your-EC2-ip:5000'
        }
        failure {
            echo 'Something went wrong. Check the logs above for errors.'
        }
    }
}
