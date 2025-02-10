def currentStackStatusAndDeploying () {
    return '''
    stack_status=$(aws cloudformation describe-stacks --stack-name user-form-app-project | jq -r '.Stacks[0].StackStatus')
    if [[ "$stack_status" == "CREATE_COMPLETE" || "$stack_status" == "UPDATE_COMPLETE" ]]; then
        echo "Stack exists, updating the stack..."
        # Capture the output of the update-stack command, even if it fails.
        update_output=$(aws cloudformation update-stack \
            --template-body file://aws/user-form-app.yaml \
            --stack-name user-form-app-project \
            --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
            --region us-east-1 2>&1) || true
        
        echo "$update_output"
        
        if echo "$update_output" | grep -q "No updates are to be performed"; then
            echo "No changes detected. Skipping update."
        else
            echo "Stack update initiated."
        fi
    else
        echo "Stack doesn't exist or is not in a complete state, creating the stack..."
        aws cloudformation deploy \
            --template-file aws/user-form-app.yaml \
            --stack-name user-form-app-project \
            --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
            --region us-east-1
    fi
    '''
}

pipeline {
    agent any

    stages {
        stage('Deploying Stack on Cloudformation') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    args "-u root --rm --entrypoint='' --network=host"
                    reuseNode true
                }
            }
            steps {
                sh """
                yum install jq -y

                # Get the current stack status
                ${currentStackStatusAndDeploying ()}
                """
            }
        }

    }
}

