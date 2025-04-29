pipeline {
    agent any

    stages {
        stage('Clean Old Artifacts') {
            steps {
                sh 'rm -rf target'
            }
        }

        stage('Build') {
            steps {
                echo 'Building..'
                sh 'mvn package'
            }
        }

        stage('Upload Artifact') {
            steps {
                echo 'Uploading to S3...'
                sh '''
                    cd target
                    aws s3 cp WebAppCal-*.war s3://project1-joke-s3/
                '''
            }
        }

        

           stage('Deploy') {
            steps {
                sh"""
                cd ansible
                ansible-playbook -i aws_ec2.yml playbook.yml -e "BUCKET_NAME=${env.BUCKET_NAME} ARTIFACT_NAME=${env.ARTIFACT_NAME}"
                """
            }
        }
    }
}