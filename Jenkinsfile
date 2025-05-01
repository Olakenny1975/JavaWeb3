pipeline {
    agent any

    environment {
        BUCKET_NAME = "project-1-ken-bucket"
        ARTIFACT = ""
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Upload Artifact') {
            steps {
                script {
                    // Find the WAR file
                    def artifactName = sh(script: "cd target && ls WebAppCal-*.war" , returnStdout: true).trim()
                    env.ARTIFACT = artifactName

                    // Upload to S3
                    sh***
                    cd target
                    aws s3 cp ${env.ARTIFACT} s3://${env.BUCKET_NAME}/
                    ***
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('ansible') {
                    sh """
                    ansible-playbook -i aws_ec2.yml playbook.yml -e "BUCKET_NAME=${env.BUCKET_NAME}" ARTIFACT_NAME=${env.ARTIFACT}"
                    """
                }
            }
        }
    }
}
