pipeline {
    agent any

    environment {
        SONARQUBE_TOKEN = credentials('sonarqube')
        FRONTEND_IMAGE = 'salhianis20/frontend:latest'
        BACKEND_IMAGE = 'salhianis20/backend:latest'
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'

        FRONTEND_DIR = 'Application-Code/frontend'
        BACKEND_DIR = 'Application-Code/backend'
        // NVD_API_KEY = credentials('NVD_API_KEY')
        // OUTPUT_DIR = 'dependency-check-report'
        PROJECT_NAME = 'MERN-App'
    }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('SonarQube Scan - Backend') {
            steps {
                dir('Application-Code/backend') {
                    withSonarQubeEnv('sonarqube') {
                        sh '''
                            sonar-scanner \
                              -Dsonar.projectKey=backend-project \
                              -Dsonar.sources=. \
                              -Dsonar.host.url=$SONAR_HOST_URL \
                              -Dsonar.login=$SONARQUBE_TOKEN
                        '''
                    }
                }
            }
        }

        stage('SonarQube Scan - Frontend') {
            steps {
                dir('Application-Code/frontend') {
                    withSonarQubeEnv('sonarqube') {
                        sh '''
                            sonar-scanner \
                              -Dsonar.projectKey=frontend-project \
                              -Dsonar.sources=. \
                              -Dsonar.host.url=$SONAR_HOST_URL \
                              -Dsonar.login=$SONARQUBE_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("${FRONTEND_IMAGE}", 'Application-Code/frontend')
                    docker.build("${BACKEND_IMAGE}", 'Application-Code/backend')
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh '''
                    mkdir -p reports
                    trivy image "${FRONTEND_IMAGE}" > reports/trivy_frontend.txt
                    trivy image "${BACKEND_IMAGE}" > reports/trivy_backend.txt
                '''
            }
        }


        stage('Push to Docker Hub') {
            steps {
                withVault([
                    vaultSecrets: [[
                        path: 'kv/dockerhub-creds',
                        secretValues: [
                            [envVar: 'DOCKER_USERNAME', vaultKey: 'username'],
                            [envVar: 'DOCKER_PASSWORD', vaultKey: 'password']
                        ]
                    ]]
                ]) {
                    script {
                        sh '''
                            echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                            docker push "${FRONTEND_IMAGE}"
                            docker push "${BACKEND_IMAGE}"
                        '''
                    }
                }
            }
        }
    }
}
