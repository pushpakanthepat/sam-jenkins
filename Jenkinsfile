pipeline {
    agent any

    parameters {
        choice(name: 'DEPLOY_STAGE', choices: ['staging', 'production'], description: 'Select the deployment stage')
        string(name: 'ARTIFACTS_BUCKET', defaultValue: 'my-artifacts-bucket', description: 'Enter the S3 bucket for artifacts')
        string(name: 'ARTIFACTS_PREFIX', defaultValue: 'my-prefix', description: 'Enter the S3 prefix for artifacts')
    }

    environment {
        AWS_DEFAULT_REGION = 'your-aws-region'
        STACK_NAME         = "my-stack-${params.DEPLOY_STAGE}"
        AWS_CREDENTIALS_ID = 'your-aws-credentials-id'
    }

    stages {
        stage('Package Application') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID, accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        sh "sam package --s3-bucket ${params.ARTIFACTS_BUCKET} --s3-prefix ${params.ARTIFACTS_PREFIX} --output-template-file out.yml"
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID, accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
                        sh "sam deploy --template-file out.yml --stack-name $STACK_NAME --parameter-overrides ParameterKey=Environment,ParameterValue=${params.DEPLOY_STAGE} --capabilities CAPABILITY_IAM"
                    }
                }
            }
        }
    }

    post {
        success {
            // Any post-success actions
        }
        failure {
            // Any post-failure actions
        }
    }
}
