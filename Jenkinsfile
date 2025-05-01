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
                    // Capture exact artifact filename (e.g., WebAppCal-0.0.7.war)
                    def artifactName = sh(
                        script: "cd target && ls WebAppCal-*.war" , returnStdout: true).trim()
                    env.ARTIFACT = artifactName

                    // Upload to S3
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
    }
}
