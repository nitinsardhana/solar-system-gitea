pipeline {
    agent any

    environment {
        MONGO_URI = 'mongodb://mongodb:27017/solar-system'
        DOCKERHUB_USERNAME = 'viraj1989'
        IMAGE_NAME = 'viraj1989/solar-system'
        IMAGE_TAG = "${BUILD_NUMBER}"
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
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                sh 'docker tag $IMAGE_NAME:$IMAGE_TAG $IMAGE_NAME:latest'
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo '========== Pushing to DockerHub =========='
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        docker login -u $DOCKER_USER -p $DOCKER_PASS
                        docker push $IMAGE_NAME:$IMAGE_TAG
                        docker push $IMAGE_NAME:latest
                    '''
                }
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
                        $IMAGE_NAME:latest
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