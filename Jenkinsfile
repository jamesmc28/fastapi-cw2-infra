pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag')
    }

    stages {

        stage('Setup Server') {
            steps {
                withCredentials([string(credentialsId: 'ansible-vault-pass', variable: 'VAULT_PASS')]) {
                    sh '''
                    echo $VAULT_PASS > vault_pass.txt
                    ansible-playbook -i ansible/inventory.ini ansible/setup.yml --vault-password-file vault_pass.txt
                    '''
                }
            }
        }

        stage('Deploy App') {
            steps {
                withCredentials([string(credentialsId: 'ansible-vault-pass', variable: 'VAULT_PASS')]) {
                    sh '''
                    echo $VAULT_PASS > vault_pass.txt
                    ansible-playbook -i ansible/inventory.ini ansible/deploy.yml \
                    --vault-password-file vault_pass.txt \
                    --extra-vars "image_tag=${IMAGE_TAG}"
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sh 'curl -f http://3.214.77.225'
            }
        }
    }
}
