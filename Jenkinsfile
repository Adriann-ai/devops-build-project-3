pipeline {
    agent any

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    if (env.GIT_BRANCH == "dev") {
                        sh "chmod +x build-dev.sh"
                        sh "./build-dev.sh"
                    } else if (env.GIT_BRANCH == "main") {
                        sh "chmod +x build-prod.sh"
                        sh "./build-prod.sh"
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
                        if (env.GIT_BRANCH == "dev") {
                            sh "chmod +x deploy-dev.sh"
                            sh "./deploy-dev.sh"
                        } else if (env.GIT_BRANCH == "main") {
                            sh "chmod +x deploy-prod.sh"
                            sh "./deploy-prod.sh"
                        }
                    }
                }
            }
        }

        stage('Deploy on Remote Server') {
            steps {
                script {
                    sshagent(['trend-key']) {
                        if (env.GIT_BRANCH == "dev") {
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@15.207.221.33 'cd /home/ubuntu/ && docker-compose pull && docker-compose up -d'"
                        } else if (env.GIT_BRANCH == "main") {
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@13.235.91.209 'cd /home/ubuntu/ && docker-compose pull && docker-compose up -d'"
                        }
                    }
                }
            }
        }

        stage('Deployment Status') {
            steps {
                script {
                    sshagent(['trend-key']) {
                        if (env.GIT_BRANCH == "dev") {
                            sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@15.207.221.33 '
                            echo "==== Container Status ===="
                            docker ps --filter "name=reactapp_dev"
                            echo "==== Last 50 Logs ===="
                            docker logs --tail 50 reactapp_dev
                            '
                            """
                        } else if (env.GIT_BRANCH == "main") {
                            sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@13.235.91.209 '
                            echo "==== Container Status ===="
                            docker ps --filter "name=reactapp_dev"
                            echo "==== Last 50 Logs ===="
                            docker logs --tail 50 reactapp_dev
                            '
                            """
                        }
                    }
                }
            }
        }

        stage('Health Check') {
            steps {
                script {
                    sshagent(['trend-key']) {
                        if (env.GIT_BRANCH == "dev") {
                            sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@65.0.87.169 '
                            if curl -s --head http://localhost:80/ | grep "200 OK" > /dev/null; then
                                echo "Dev App is healthy ✅"
                            else
                                echo "Dev App is NOT healthy ❌"
                            fi
                            '
                            """
                        } else if (env.GIT_BRANCH == "main") {
                            sh """
                            ssh -o StrictHostKeyChecking=no ubuntu@13.201.131.251 '
                            if curl -s --head http://localhost:80/ | grep "200 OK" > /dev/null; then
                                echo "Prod App is healthy ✅"
                            else
                                echo "Prod App is NOT healthy ❌"
                            fi
                            '
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Deployment & Monitoring finished!"
        }
    }
}
