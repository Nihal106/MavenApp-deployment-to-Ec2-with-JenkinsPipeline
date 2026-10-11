pipeline {
    agent any

    tools {
        maven 'maven-3.9'
        jdk 'JDK17'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Nihal106/MavenApp-deployment-to-Ec2-with-JenkinsPipeline.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }


stage('Deploy to EC2') {
    environment {
        EC2_HOST = '100.24.57.121'
    }
    steps {
        sshagent(['ssh-ubuntu']) {
            sh '''
                set -e

                echo "Copying artifact to EC2..."

                ssh -o StrictHostKeyChecking=no \
                    ubuntu@$EC2_HOST \
                    'sudo mkdir -p /opt/app &&
                     sudo chown ubuntu:ubuntu /opt/app'

                scp -o StrictHostKeyChecking=no \
                    target/demo-1.0.0.jar \
                    ubuntu@$EC2_HOST:/opt/app/

                echo "Starting application on EC2..."

                ssh -o StrictHostKeyChecking=no \
                    ubuntu@$EC2_HOST \
                    'pkill -f "[d]emo-1.0.0.jar" || true
                     nohup java -jar /opt/app/demo-1.0.0.jar \
                       > /opt/app/app.log 2>&1 < /dev/null &'

                echo "Deployment commands completed."
            '''
        }
    }
}

    }

    post {
        success {
            echo "Deployment completed successfully."
        }
        failure {
            echo "Deployment failed. Check Jenkins or EC2 logs."
        }
    }
}
