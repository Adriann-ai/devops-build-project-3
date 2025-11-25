pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (env.GIT_BRANCH == "origin/dev") {
                        sh "chmod +x build/build-dev.sh"
                        sh "./build/build-dev.sh"
                    } else if (env.GIT_BRANCH == "origin/prod") {
                        sh "chmod +x build/build-prod.sh"
                        sh "./build/build-prod.sh"
                    } else {
                        error "Sorry Branch not supported: ${env.GIT_BRANCH}"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'doc-cred', url: 'https://index.docker.io/v1/') {
                        if (env.GIT_BRANCH == "origin/dev") {
                            sh "chmod +x deploy/deploy-dev.sh"
                            sh "./deploy/deploy-dev.sh"
                        } else if (env.GIT_BRANCH == "origin/prod") {
                            sh "chmod +x deploy/deploy-prod.sh"
                            sh "./deploy/deploy-prod.sh"
                        }
                    }
                }
            }
        }

        stage('Deploy on Remote Server') {
            steps {
                script {
                    sshagent(['remote-server-key']) {
                        if (env.GIT_BRANCH == "origin/dev") {
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@13.233.146.116 'cd /home/ubuntu/dev && docker compose pull && docker compose up -d'"
                        } else if (env.GIT_BRANCH == "origin/prod") {
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@13.233.146.116 'cd /home/ubuntu/prod && docker compose pull && docker compose up -d'"
                        }
                    }
                }
            }
        }
    }
}
