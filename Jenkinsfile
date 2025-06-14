// pipeline {
//     agent any

//     environment {
//         SONARQUBE_TOKEN = credentials('sonarqube')
//         FRONTEND_IMAGE = 'salhianis20/frontend:latest'
//         BACKEND_IMAGE = 'salhianis20/backend:latest'
//         DOCKERHUB_CREDENTIALS = 'docker-hub-credentials'

//         // FRONTEND_DIR = 'Application-Code/frontend'
//         // BACKEND_DIR = 'Application-Code/backend'
//         // OUTPUT_DIR = 'dependency-check-report'
//         // PROJECT_NAME = 'MERN-App'
//     }

//     stages {
//         stage('Checkout') {
//             steps { checkout scm }
//         }



//         // stage('Run Dependency-Check') {
//         //     steps {
//         //         script {
//         //             // Ensure the report directory exists
//         //             sh "mkdir -p ${OUTPUT_DIR}"

//         //             // Run OWASP Dependency-Check on frontend
//         //             sh """
//         //             docker run --rm \
//         //                 -v "\$PWD":/src \
//         //                 -v "\$PWD/${OUTPUT_DIR}:/report" \
//         //                 owasp/dependency-check \
//         //                 --project "${PROJECT_NAME}-frontend" \
//         //                 --scan "/src/${FRONTEND_DIR}" \
//         //                 --format "ALL" \
//         //                 --out /report
//         //             """

//         //             // Run OWASP Dependency-Check on backend
//         //             sh """
//         //             docker run --rm \
//         //                 -v "\$PWD":/src \
//         //                 -v "\$PWD/${OUTPUT_DIR}:/report" \
//         //                 owasp/dependency-check \
//         //                 --project "${PROJECT_NAME}-backend" \
//         //                 --scan "/src/${BACKEND_DIR}" \
//         //                 --format "ALL" \
//         //                 --out /report
//         //             """
//         //         }
//         //     }
//         // }

//         // stage('Publish HTML Report') {
//         //     steps {
//         //         publishHTML([
//         //             reportDir: "${OUTPUT_DIR}",
//         //             reportFiles: 'dependency-check-report.html',
//         //             reportName: 'OWASP Dependency-Check Report'
//         //         ])
//         //     }
//         // }

//         stage('SonarQube Scan - Backend') {
//             steps {
//                 dir('Application-Code/backend') {
//                     withSonarQubeEnv('sonarqube') {
//                         sh '''
//                             sonar-scanner \
//                               -Dsonar.projectKey=backend-project \
//                               -Dsonar.sources=. \
//                               -Dsonar.host.url=$SONAR_HOST_URL \
//                               -Dsonar.login=$SONARQUBE_TOKEN
//                         '''
//                     }
//                 }
//             }
//         }

//         stage('SonarQube Scan - Frontend') {
//             steps {
//                 dir('Application-Code/frontend') {
//                     withSonarQubeEnv('sonarqube') {
//                         sh '''
//                             sonar-scanner \
//                               -Dsonar.projectKey=frontend-project \
//                               -Dsonar.sources=. \
//                               -Dsonar.host.url=$SONAR_HOST_URL \
//                               -Dsonar.login=$SONARQUBE_TOKEN
//                         '''
//                     }
//                 }
//             }
//         }

//         stage('Build Docker Images') {
//             steps {
//                 script {
//                     docker.build("${FRONTEND_IMAGE}", 'Application-Code/frontend')
//                     docker.build("${BACKEND_IMAGE}", 'Application-Code/backend')
//                 }
//             }
//         }

//         stage('Trivy Scan') {
//             steps {
//                 sh '''
//                     mkdir -p reports
//                     trivy image "${FRONTEND_IMAGE}" > reports/trivy_frontend.txt
//                     trivy image "${BACKEND_IMAGE}" > reports/trivy_backend.txt
//                 '''
//             }
//         }

//         stage('Push to Docker Hub') {
//             steps {
//                 script {
//                     docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS) {
//                         docker.image("${FRONTEND_IMAGE}").push()
//                         docker.image("${BACKEND_IMAGE}").push()
//                     }
//                 }
//             }
//         }
//     }
// }





pipeline {
    agent any

    environment {
        SONARQUBE_TOKEN = credentials('sonarqube')
        FRONTEND_IMAGE = 'salhianis20/frontend:latest'
        BACKEND_IMAGE = 'salhianis20/backend:latest'
        // FRONTEND_DIR = 'Application-Code/frontend'
        // BACKEND_DIR = 'Application-Code/backend'
        // OUTPUT_DIR = 'dependency-check-report'
        // PROJECT_NAME = 'MERN-App'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        /*
        stage('Run Dependency-Check') {
            steps {
                script {
                    sh "mkdir -p ${OUTPUT_DIR}"

                    // Frontend
                    sh """
                    docker run --rm \
                        -v "\$PWD":/src \
                        -v "\$PWD/${OUTPUT_DIR}:/report" \
                        owasp/dependency-check \
                        --project "${PROJECT_NAME}-frontend" \
                        --scan "/src/${FRONTEND_DIR}" \
                        --format "ALL" \
                        --out /report
                    """

                    // Backend
                    sh """
                    docker run --rm \
                        -v "\$PWD":/src \
                        -v "\$PWD/${OUTPUT_DIR}:/report" \
                        owasp/dependency-check \
                        --project "${PROJECT_NAME}-backend" \
                        --scan "/src/${BACKEND_DIR}" \
                        --format "ALL" \
                        --out /report
                    """
                }
            }
        }

        stage('Publish HTML Report') {
            steps {
                publishHTML([
                    reportDir: "${OUTPUT_DIR}",
                    reportFiles: 'dependency-check-report.html',
                    reportName: 'OWASP Dependency-Check Report'
                ])
            }
        }
        */

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
                script {
                    withVault(
                        configuration: [
                            disableChildPoliciesOverride: false,
                            timeout: 60,
                            vaultCredentialId: 'vault-cred',
                            vaultUrl: 'http://127.0.0.1:8200'
                        ],
                        vaultSecrets: [[
                            path: 'secret/cubbyhole/dockerhub-creds',
                            secretValues: [
                                [envVar: 'DOCKERHUB_USERNAME', vaultKey: 'username'],
                                [envVar: 'DOCKERHUB_PASSWORD', vaultKey: 'password']
                            ]
                        ]]
                    ) {
                        sh '''
                            echo "$DOCKERHUB_PASSWORD" | docker login -u "$DOCKERHUB_USERNAME" --password-stdin
                            docker push ${FRONTEND_IMAGE}
                            docker push ${BACKEND_IMAGE}
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.txt', allowEmptyArchive: true
        }
    }
}

