pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                sh"""
                ls src
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