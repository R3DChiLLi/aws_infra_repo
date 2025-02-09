pipeline {
    agent any

    stages {
        agent {
            docker {
                image 'amazon/aws-cli'
                args "--rm --entrypoint='' --network=host"
                reuseNode true
            }
        }
        }
        stage('Deploying Stack on Cloudformation') {
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
