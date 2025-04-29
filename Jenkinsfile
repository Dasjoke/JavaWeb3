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

        stage('Verify Artifact') {
            steps {
                echo '🔍 Listing target directory to check artifact...'
                sh 'ls -lh target'
            }
        }

        stage('Set Artifact Name') {
            steps {
                script {
                    // Use find to locate the WAR file and assign it to the artifactName variable
                    def artifactName = sh(
                        script: "find target -type f -name 'WebAppCal-*.war' -print -quit",
                        returnStdout: true
                    ).trim()

                    if (!artifactName) {
                        error("❌ No WAR artifact found in target/. Check Maven build output.")
                    }

                    // Extract the filename from the path
                    env.ARTIFACT_NAME = artifactName.split("/").last()
                    echo "✅ Found artifact: ${env.ARTIFACT_NAME}"
                }
            }
        }

        stage('Upload Artifact') {
            steps {
                echo "☁️ Uploading ${env.ARTIFACT_NAME} to S3 bucket ${env.BUCKET_NAME}..."
                sh """
                    aws s3 cp target/${env.ARTIFACT_NAME} s3://${env.BUCKET_NAME}/
                """
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


