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
                    // Find the WAR file and assign it to a Groovy variable
                    def artifactName = sh(
                        script: "ls target/WebAppCal*.war",
                        returnStdout: true
                    ).trim()

                    // Set the artifact name correctly (no additional path)
                    env.ARTIFACT_NAME = "target/${artifactName}"

                    // Upload it to S3 using the full path
                    sh "aws s3 cp ${env.ARTIFACT_NAME} s3://${env.BUCKET_NAME}/"
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

