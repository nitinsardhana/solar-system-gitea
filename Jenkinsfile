pipeline {
    agent any

    environment {
        MONGO_URI = 'mongodb://localhost:27017/solar-system'
    }

    stages {

        stage('Checkout') {
            steps {
                echo '========== Checking out code =========='
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo '========== Installing npm packages =========='
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo '========== Running Tests =========='
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '========== Building Docker Image =========='
                sh 'docker build -t solar-system:latest .'
            }
        }

    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
        always {
            echo 'Pipeline finished'
        }
    }
}