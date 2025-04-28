pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                sh"""
                mvn package
                """
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
