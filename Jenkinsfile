<<<<<<< HEAD
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Upload Artifact') {
            steps {
                sh '''
                    cd target
                    aws s3 cp WebAppCal-*.war s3://proj1-bucket-ay/
                '''
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
=======
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
>>>>>>> 35a0a0c (chore: add jenkinsfile)
