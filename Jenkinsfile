pipeline {
    agent any

    stages {
        stage('Deploying Stack on Cloudformation') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    args "--rm --entrypoint='' --network=host"
                    reuseNode true
                }
            }
            steps {
                sh '''
                aws cloudformation deploy \
                --template-file aws/user-form-app.yaml \
                --stack-name user-form-app-project \
                --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
                --region us-east-1 \
                '''
            }
        }
        }
}
