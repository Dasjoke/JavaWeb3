pipeline {
    agent any

    environment {
        BUCKET_NAME = 'project1-joke-s3'
        ARTIFACT_NAME = ''  // Placeholder, will be set dynamically
    }

    stages {
        stage('Clean Old Artifacts') {
            steps {
                sh 'rm -rf target'
            }
        }

        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mvn package'
            }
        }

        stage('Set Artifact Name') {
            steps {
                script {
                    // Get the actual filename and store it
                    def artifactName = sh(
                        script: "ls target/WebAppCal-*.war | xargs -n 1 basename",
                        returnStdout: true
                    ).trim()
                    
                    env.ARTIFACT_NAME = artifactName
                }
            }
        }

        stage('Upload Artifact') {
            steps {
                echo "Uploading ${env.ARTIFACT_NAME} to S3..."
                sh """
                    aws s3 cp target/${env.ARTIFACT_NAME} s3://${env.BUCKET_NAME}/
                """
            }
        }

        stage('Deploy') {
            steps {
                echo "Deploying ${env.ARTIFACT_NAME} using Ansible..."
                sh """
                    cd ansible
                    ansible-playbook -i aws_ec2.yml playbook.yml \
                      -e "BUCKET_NAME=${env.BUCKET_NAME} ARTIFACT_NAME=${env.ARTIFACT_NAME}"
                """
            }
        }
    }
}
