pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'SonarQubeServer'   // Update this to your Jenkins SonarQube config name
        FRONTEND_IMAGE = 'salhianis20/frontend:latest'
        BACKEND_IMAGE = 'salhianis20/backend:latest'
        DOCKERHUB_CREDENTIALS = 'docker-hub-credentials' // Update this to your Jenkins credentials ID
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        // stage('OWASP Dependency Check') {
        //     steps {
        //         sh '''
        //             mkdir -p reports/frontend
        //             mkdir -p reports/backend
        
        //             docker run --rm \
        //                 -v $(pwd)/Application-Code/frontend:/src \
        //                 -v $(pwd)/reports/frontend:/report \
        //                 owasp/dependency-check \
        //                 --scan /src \
        //                 --format "ALL" \
        //                 --out /report
        
        //             docker run --rm \
        //                 -v $(pwd)/Application-Code/backend:/src \
        //                 -v $(pwd)/reports/backend:/report \
        //                 owasp/dependency-check \
        //                 --scan /src \
        //                 --format "ALL" \
        //                 --out /report
        //         '''
        //     }
        // }


        // stage('SonarQube Scan - Frontend') {
        //     steps {
        //         dir('frontend') {
        //             withSonarQubeEnv(SONARQUBE_ENV) {
        //                 sh 'sonar-scanner -Dsonar.projectKey=frontend -Dsonar.sources=.'
        //             }
        //         }
        //     }
        // }

        // stage('SonarQube Scan - Backend') {
        //     steps {
        //         dir('backend') {
        //             withSonarQubeEnv(SONARQUBE_ENV) {
        //                 sh 'sonar-scanner -Dsonar.projectKey=backend -Dsonar.sources=.'
        //             }
        //         }
        //     }
        // }

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
                    trivy image ${FRONTEND_IMAGE} > reports/trivy_frontend.txt
                    trivy image ${BACKEND_IMAGE} > reports/trivy_backend.txt
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

}
