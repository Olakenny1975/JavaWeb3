def artifactName = ""  // Declare globally
pipeline {
    agent any
    environment {
        BUCKET_NAME = "project-1-ken-bucket"
    }
    stages {
        stage('Clean old Artifacts') {
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
                    // Capture WAR file name
                    artifactName = sh(script: "cd target && ls WebAppCal-*.war", returnStdout: true).trim()
                    // Upload to S3
                    sh """
                        cd target
                        aws s3 cp ${artifactName} s3://${env.BUCKET_NAME}/
                    """
                }
            }
        }
        stage('Deploy') {
            steps {
                sh """
                    cd ansible
                    ansible-playbook -i aws_ec2.yml playbook.yml \
                        -e "BUCKET_NAME=${env.BUCKET_NAME} ARTIFACT_NAME=${artifactName}"
                """
            }
        }
    }
}






