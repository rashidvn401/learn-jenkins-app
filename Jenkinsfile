pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.2.$BUILD_ID"
        AWS_DEFAULT_REGION = 'eu-north-1'
    }

    stages {
/*
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Small changes"
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
*/
        stage ('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root --entrypoint=''"
                }
            }

            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                sh '''
                    aws --version
                    yum install jq -y
                    LATEST_TD_REVISION=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json | jq '.taskDefinition.revision')
                    echo $LATEST_TD_REVISION
                    aws ecs update-service --cluster determined-wolf-LearnJenkins-pipeline-demo --service determined-wolf-LearnJenkins-pipeline-demo-service-Prod --task-definition determined-wolf-LearnJenkins-pipeline-demo:$LATEST_TD_REVISION
                '''
                }
            }
        }
    }
}