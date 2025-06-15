pipeline {
    agent any

    environment {
        VAULT_CREDENTIALS_ID = 'vault-cred'
        SONARQUBE_TOKEN = credentials('sonarqube')
        FRONTEND_IMAGE = 'salhianis20/frontend:latest'
        BACKEND_IMAGE = 'salhianis20/backend:latest'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Read secret from Vault') {
            steps {
                withVault(
                    vaultSecrets: [
                        [
                            path: 'secret/dockerhub-creds',
                            secretValues: [
                                [envVar: 'USERNAME', vaultKey: 'username'],
                                [envVar: 'PASSWORD', vaultKey: 'password']
                            ]
                        ]
                    ],
                    vaultCredentialId: "${VAULT_CREDENTIALS_ID}",
                    vaultUrl: 'http://127.0.0.1:8200'
                ) {
                    sh 'echo "Username is $USERNAME"'
                }
            }
        }

        // You can uncomment and include other stages here as needed
    }
}
