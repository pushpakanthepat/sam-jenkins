pipeline {
    agent any

    tools{
        nodejs 'node'
    }

   environment {
        AWS_DEFAULT_REGION = 'ap-southeast-2'
        STACK_NAME         = "uatr-jenkins-test"
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_JENKINS')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY_JENKINS')
        ARTIFACTS_BUCKET = credentials('S3_BUCKET')
        ARTIFACTS_PREFIX = 'uatr-jenkins-test'
    }

    stages {
        stage('Unit Test') {
            steps {
                script {
                    sh "npm i -g recursive-install"
                    sh "npm-recursive-install --rootDir=src"
                    dir("src/books/create") {
                        sh "npm test"
                    }
                    dir("src/books/get-all") {
                        sh "npm test"
                    }
                }
            }
        }
        
        stage('Package Application') {
            steps {
                script {
                        sh "sam build"
                        sh "sam package --s3-bucket ${params.ARTIFACTS_BUCKET} --s3-prefix ${params.ARTIFACTS_PREFIX} --output-template-file template.yml"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                        sh "sam deploy --template-file template.yml --stack-name $STACK_NAME --parameter-overrides ParameterKey=Stage,ParameterValue=${params.DEPLOY_STAGE} --capabilities CAPABILITY_IAM"
                }
            }
        }
    }
}
