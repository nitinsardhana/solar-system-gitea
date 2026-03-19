pipeline {
    agent any

    environment {
        MONGO_URI = 'mongodb://mongodb:27017/solar-system'
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
                sh 'export MONGO_URI="mongodb://mongodb:27017/solar-system" && npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo '========== Building Docker Image =========='
                sh 'docker build -t solar-system:latest .'
            }
        }

        stage('Deploy') {
            steps {
                echo '========== Deploying Application =========='
                sh '''
                    docker stop solar-app || true
                    docker rm solar-app || true
                    docker run -d \
                        --name solar-app \
                        --network jenkins-network \
                        -p 3000:3000 \
                        -e MONGO_URI="mongodb://mongodb:27017/solar-system" \
                        solar-system:latest
                '''
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
    }
}