pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'SonarQubeServer'   // Update this to your Jenkins SonarQube config name
        FRONTEND_IMAGE = 'salhianis20/frontend:latest'
        BACKEND_IMAGE = 'salhianis20/backend:latest'
        DOCKERHUB_CREDENTIALS = 'dockerhub-creds' // Update this to your Jenkins credentials ID
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('OWASP Dependency Check') {
            steps {
                sh '''
                    mkdir -p reports
                    docker run --rm \
                        -v $(pwd)/frontend:/src/frontend \
                        -v $(pwd)/reports:/report \
                        owasp/dependency-check \
                        --scan /src/frontend \
                        --format "ALL" \
                        --out /report/frontend

                    docker run --rm \
                        -v $(pwd)/backend:/src/backend \
                        -v $(pwd)/reports:/report \
                        owasp/dependency-check \
                        --scan /src/backend \
                        --format "ALL" \
                        --out /report/backend
                '''
            }
        }

        stage('SonarQube Scan - Frontend') {
            steps {
                dir('frontend') {
                    withSonarQubeEnv(SONARQUBE_ENV) {
                        sh 'sonar-scanner -Dsonar.projectKey=frontend -Dsonar.sources=.'
                    }
                }
            }
        }

        stage('SonarQube Scan - Backend') {
            steps {
                dir('backend') {
                    withSonarQubeEnv(SONARQUBE_ENV) {
                        sh 'sonar-scanner -Dsonar.projectKey=backend -Dsonar.sources=.'
                    }
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("${FRONTEND_IMAGE}", 'frontend')
                    docker.build("${BACKEND_IMAGE}", 'backend')
                }
            }
        }

        stage('Trivy Scan') {
            steps {
                sh '''
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --exit-code 1 --severity HIGH,CRITICAL ${FRONTEND_IMAGE}
                    docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --exit-code 1 --severity HIGH,CRITICAL ${BACKEND_IMAGE}
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
                        docker.image("${FRONTEND_IMAGE}").push()
                        docker.image("${BACKEND_IMAGE}").push()
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/**/*.*', allowEmptyArchive: true
        }
        failure {
            echo 'Build failed. Please check logs.'
        }
    }
}
