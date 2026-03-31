pipeline {
    agent any

    stages {

        stage('Checkout Repo B') {
            steps {
                git 'https://github.com/jamesmc28/fastapi-cw2-infra.git'
            }
        }

        stage('Setup Server') {
            steps {
                sh 'ansible-playbook -i ansible/inventory.ini ansible/setup.yml'
            }
        }

        stage('Deploy App') {
            steps {
                sh 'ansible-playbook -i ansible/inventory.ini ansible/deploy.yml'
            }
        }
    }
}
