pipeline {
    agent any
    stages {
        stage('Git Checkouts-adrian-s') {
            steps {
                git branch: 'dev', url: 'https://github.com/Adriann-ai/devops-build-project-3.git'
            }
        }
        stage('Docker Build and Tag') {
            steps {
                sh 'chmod +x build.sh'
                sh './build.sh'
            }
        }
        stage('Docker Push to DockerHub') {
            steps {
                withDockerRegistry([credentialsId: 'doc-cred', url: 'https://index.docker.io/v1/']) {
                    sh 'chmod +x deploy.sh'
                    sh './deploy.sh'
                }
            }
        }
        stage('Run Docker Compose on Remote Server') {
            steps {
                sshagent(credentials: ['remote-server-key']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@13.233.146.116 'cd /home/ubuntu && docker-compose pull && docker-compose up -d --remove-orphans'"
                }
            }
        }
    }
}
