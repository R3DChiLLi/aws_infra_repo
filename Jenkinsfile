def currentStackStatusAndDeploying() {
    return '''
    stack_status=$(aws cloudformation describe-stacks --stack-name user-form-app-project | jq -r '.Stacks[0].StackStatus')
    
    echo "Current CloudFormation Stack Status: ${stack_status}"
    
    if [[ "$stack_status" == "CREATE_COMPLETE" || "$stack_status" == "UPDATE_COMPLETE" ]]; then
        echo "Stack exists, updating the stack..."
        
        update_output=$(aws cloudformation update-stack \
            --template-body file://aws/user-form-app.yaml \
            --stack-name user-form-app-project \
            --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
            --region us-east-1 2>&1) || true
        
        echo "$update_output"
        
        if echo "$update_output" | grep -q "No updates are to be performed"; then
            echo "No changes detected. Skipping update."
        else
            echo "Stack update initiated. Waiting for update to complete..."
            aws cloudformation wait stack-update-complete --stack-name user-form-app-project --region us-east-1
            echo "Stack update completed."
        fi
        
    elif [[ "$stack_status" == "CREATE_IN_PROGRESS" || "$stack_status" == "REVIEW_IN_PROGRESS" ]]; then
        echo "Stack is currently being created (status: ${stack_status}). Aborting deployment." >&2
        exit 1
        
    else
        echo "Stack doesn't exist or is in an unexpected state, creating the stack..."
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
                ${currentStackStatusAndDeploying ()}
                """
            }
        }

    }
}

