pipeline {
    agent any

    environment {
        VAULT_CREDENTIALS_ID = 'vault-cred'  // used only if needed elsewhere
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Read secret from Vault') {
            steps {
                script {
                    withVault([
                        vaultSecrets: [[
                            path: 'secret/dockerhub-creds',
                            secretValues: [
                                [envVar: 'USERNAME', vaultKey: 'username'],
                                [envVar: 'PASSWORD', vaultKey: 'password']
                            ]
                        ]]
                    ]) {
                        sh 'echo "Username is $USERNAME"'
                        // use $USERNAME and $PASSWORD here safely
                    }
                }
            }
        }
    }
}
