pipeline {
    agent any
    environment {
        BUCKET_NAME = 'project1-joke-s3'
    }

    stages {
        stage('Clean Old Artifacts') {
            steps {
                sh 'rm -rf target'
            }
        }

        stage('Build') {
            steps {
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

                    // Export artifact name for downstream use (e.g., Ansible)
                    env.ARTIFACT_NAME = artifactName

                    // Upload to S3
                    sh """
                    aws s3 cp target/${artifactName} s3://${env.BUCKET_NAME}/
                    """
                }
            }
        }
    }
}

