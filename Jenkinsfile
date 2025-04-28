pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                sh """
                    mvn package
                """
            }
        }

        stage('Upload Artifact') {
            steps {
                echo 'Uploading to S3...'
                sh """
                    cd target
                    aws s3 cp WebAppCal-*.war s3://project1-joke-s3/
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying....'
                // Add actual deployment commands here
            }
        }
    }
}
