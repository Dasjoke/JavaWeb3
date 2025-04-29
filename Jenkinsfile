pipeline {
    agent any

    environment {
        BUCKET_NAME = 'project1-joke-s3'
        ARTIFACT_NAME = '' // Will be set dynamically
    }

    stages {

        stage('Clean Old Artifacts') {
            steps {
                echo '🧹 Cleaning old build artifacts...'
                sh 'rm -rf target'
            }
        }

        stage('Build') {
            steps {
                echo '🔨 Building project...'
                sh 'mvn package'
            }
        }

        stage('Upload Artifact') {
            steps {
                script {
                    // Find the WAR file
                    def artifactName = sh(
                        script: "cd target && ls WebAppCal-*.war",
                        returnStdout: true
                    ).trim()
                    
                    // Set the artifact name as an environment variable
                    env.ARTIFACT_NAME = artifactName
                    
                    // Upload it to S3 (replace BUCKET_NAME with your actual bucket name)
                    sh """
                        cd target
                        aws s3 cp ${env.ARTIFACT_NAME} s3://${BUCKET_NAME}/
                    """
                }
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Deploying ${env.ARTIFACT_NAME} using Ansible..."
                sh """
                    cd ansible
                    ansible-playbook -i aws_ec2.yml playbook.yml -e "BUCKET_NAME=${env.BUCKET_NAME} ARTIFACT_NAME=${env.ARTIFACT_NAME}"
                """
            }
        }
    }
}
