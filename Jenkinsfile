pipeline {
    agent any
    environment {
        BUCKET_NAME = 'jenkins-assigmnet-4'
        AWS_REGION = 'ap-south-1'
    }

    stages {
        stage('Checkout') {
            steps {
                // This pulls the code from GitHub automatically
                checkout scm
            }
        }
        
        stage('Deploy and Configure') {
            steps {
                // Ensure this ID matches what you saved in Jenkins Credentials
                withCredentials([aws(credentialsId: 'aws-s3-creds', region: "${AWS_REGION}")]) {
                    sh '''
                        # 1. Upload files to S3
                        aws s3 sync . s3://${BUCKET_NAME} --delete
                        
                        # 2. Enable Static Website Hosting
                        aws s3api put-bucket-website --bucket ${BUCKET_NAME} \
                            --website-configuration '{"IndexDocument":{"Suffix":"index.html"}}'
                        
                        # 3. Apply Public Read Policy
                        aws s3api put-bucket-policy --bucket ${BUCKET_NAME} \
                            --policy '{"Version":"2012-10-17","Statement":[{"Effect":"Allow","Principal":"*","Action":"s3:GetObject","Resource":"arn:aws:s3:::'"${BUCKET_NAME}"'/*"}]}'
                    '''
                }
            }
        }
    }
}
