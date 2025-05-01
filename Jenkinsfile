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
                sshagent(['ec2-ssh-key-id']) {  // Replace with your SSH credentials ID in Jenkins
                    sh """
                        ssh -o StrictHostKeyChecking=no ec2-user@your-ec2-public-ip << EOF
                            echo "Stopping Tomcat..."
                            sudo systemctl stop tomcat

                            echo "Removing old WAR and exploded app..."
                            sudo rm -rf /opt/tomcat/webapps/ROOT*

                            echo "Downloading WAR from S3..."
                            aws s3 cp s3://${BUCKET_NAME}/${ARTIFACT} /opt/tomcat/webapps/ROOT.war

                            echo "Starting Tomcat..."
                            sudo systemctl start tomcat

                            echo "Deployment complete."
                        EOF
                    """
                }
            }
        }
    }
}
