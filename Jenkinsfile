pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'shuvajit04'
        IMAGE_BACKEND  = "${DOCKERHUB_USER}/my-team-backend"
        IMAGE_TAG      = "${BUILD_NUMBER}"
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Install') {
            parallel {
                stage('Backend')  { steps { dir('backend')  { sh 'npm ci' } } }
                stage('Frontend') { steps { dir('frontend') { sh 'npm ci' } } }
            }
        }

        stage('Test') {
            steps {
                dir('backend')  { sh 'npm test --if-present' }
                dir('frontend') { sh 'npm test --if-present' }
            }
        }

        stage('Build') {
            steps {
                dir('frontend') { sh 'npm run build' }
                sh 'docker build -t $IMAGE_BACKEND:$IMAGE_TAG -t $IMAGE_BACKEND:latest backend'
            }
        }

        stage('Push Image') {
            when { branch 'main' }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh '''
                      echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
                      docker push $IMAGE_BACKEND:$IMAGE_TAG
                      docker push $IMAGE_BACKEND:latest
                    '''
                }
            }
        }

        stage('Deploy (CD)') {
            when { branch 'main' }
            steps {
                sh 'docker compose up -d --build'
                sh 'docker compose ps'
            }
        }
    }

    post {
        success { echo 'Pipeline succeeded' }
        failure { echo 'Pipeline failed' }
        always  { sh 'docker logout || true' }
    }
}
