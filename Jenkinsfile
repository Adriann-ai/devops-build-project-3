pipeline {
    agent any

    stages {

        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build and Tag') {
            steps {
                sh './build.sh'
            }
        }

        stage('Docker Push to Hub') {
            steps {
                sh './deploy.sh'
            }
        }

        stage('Run Docker Compose on Remote Server') {
            steps {
                sshagent(credentials: ['remote-server-key']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@13.233.146.116 '
                            cd /home/ubuntu &&
                            docker-compose pull &&
                            docker-compose up -d --remove-orphans
                        '
                    """
                }
            }
        }
    }
}
