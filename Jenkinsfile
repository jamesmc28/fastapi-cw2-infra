pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_TAG', defaultValue: 'latest', description: 'Docker image tag')
    }

    stages {

        stage('Provision EC2') {
            steps {
                // your existing code
            }
        }

        stage('Setup Server') {
            steps {
                // your existing code
            }
        }

        stage('Deploy App') {
            steps {
                // your existing code
            }
        }

        //)
        stage('Health Check') {
            steps {
                sh '''
                IP=$(awk 'NR==2 {print $1}' ansible/inventory.ini)

                echo "Waiting for app to become available..."

                i=1
                while [ $i -le 10 ]
                do
                    if curl -f http://$IP; then
                        echo "App is up!"
                        exit 0
                    fi

                    echo "Retrying... ($i)"
                    sleep 5
                    i=$((i+1))
                done

                echo "App failed to start"
                exit 1
                '''
            }
        }
    }
}
