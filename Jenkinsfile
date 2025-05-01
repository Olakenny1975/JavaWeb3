pipeline {
    agent any

    environment {
        BUCKET_NAME = "project-1-ken-bucket"
        ARTIFACT = "" // Will be set dynamically
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
                    // Get the actual WAR filename (e.g., WebAppCal-0.0.7.war)
                    def artifactName = sh(
                        script: "ls target/WebAppCal-*.war | xargs -n 1 basename",
                        returnStdout: true
                    ).trim()

                    // Set artifact name to environment variable for later use
                    env.ARTIFACT = artifactName

                    // Upload WAR to S3 bucket
                    sh "aws s3 cp target/${env.ARTIFACT} s3://${env.BUCKET_NAME}/${env.ARTIFACT}"
                }
            }
        }
             stage('Deploy') {
            steps {
                dir('ansible') {
                    sh """
                        ansible-playbook -i aws_ec2.yml playbook.yml \\
                            -e "BUCKET_NAME=${env.BUCKET_NAME}" \\
                            -e "ARTIFACT_NAME=${env.ARTIFACT}"
                    """
                }
            }
        }

       
