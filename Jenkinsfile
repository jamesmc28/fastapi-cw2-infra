pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag')
    }

    stages {

        stage('Provision EC2') {
            steps {
                script {
                    def ip = sh(
                        script: "ansible-playbook ansible/provision_ec2.yml | grep 'EC2 Public IP' | awk '{print \\$NF}' | tr -d '\"'",
                        returnStdout: true
                    ).trim()

                    echo "New EC2 IP: ${ip}"

                    writeFile file: 'ansible/inventory.ini', text: """[web]
${ip} ansible_user=ubuntu ansible_ssh_private_key_file=/var/lib/jenkins/.ssh/Devops2.pem
"""
                }
            }
        }

        stage('Setup Server') {
            steps {
                withCredentials([string(credentialsId: 'ansible-vault-pass', variable: 'VAULT_PASS')]) {
                    sh '''
                    echo $VAULT_PASS > vault_pass.txt
                    ansible-playbook -i ansible/inventory.ini ansible/setup.yml \
                    --vault-password-file vault_pass.txt
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
                sh '''
                IP=$(awk 'NR==2 {print $1}' ansible/inventory.ini)
                echo "Checking app on $IP"
                curl -f http://$IP
                '''
            }
        }
    }
}
