pipeline {
    agent any

    environment {
        IMAGE_NAME = "klwya/netsoft:0.0.1"
        DOCKERHUB_CREDENTIALS_ID = "4f9c9400-6713-4a74-aae7-a64d649eb516"
    }

    stages {
        stage('Build') {
            steps {
                sh 'python3 --version'
            }
        }

        stage('Lint') {
            steps {
                sh '''
                set -e
                python3 -m venv venv
                . ./venv/bin/activate
                pip install --upgrade pip
                pip install flake8
                flake8 app.py
                '''
            }
        }

        stage('Format') {
            steps {
                sh '''
                set -e
                . ./venv/bin/activate
                pip install --upgrade pip
                pip install black
                black --check app.py
                '''
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                set -e
                docker --version
                docker build -t "$IMAGE_NAME" .
                '''
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKERHUB_CREDENTIALS_ID}",
                    usernameVariable: 'DOCKERHUB_USER',
                    passwordVariable: 'DOCKERHUB_PASS'
                )]) {
                    sh '''
                    set -e
                    echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                    docker push "$IMAGE_NAME"
                    docker logout
                    '''
                }
            }
        }
    }
}

