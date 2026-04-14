pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag')
    }

    stages {

        stage('Setup Server') {
            steps {
                sh 'ansible-playbook -i ansible/inventory.ini ansible/setup.yml'
            }
        }

        stage('Deploy App') {
            steps {
                sh "ansible-playbook -i ansible/inventory.ini ansible/deploy.yml --extra-vars 'image_tag=${params.IMAGE_TAG}'"
            }
        }

        stage('Health Check') {
            steps {
                sh 'curl -f http://3.214.77.225'
            }
        }
    }
}
