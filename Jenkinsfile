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
                    if (env.GIT_BRANCH == "dev") {
                        sh "chmod +x build-dev.sh"
                        sh "./build-dev.sh"
                    } else if (env.GIT_BRANCH == "prod") {
                        sh "chmod +x build-prod.sh"
                        sh "./build-prod.sh"
                    } else {
                        error "Branch not supported: ${env.GIT_BRANCH}"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'doc-cred', url: 'https://index.docker.io/v1/') {
                        if (env.GIT_BRANCH == "dev") {
                            sh "docker push adreann/reactapp_dev:latest"
                        } else if (env.GIT_BRANCH == "prod") {
                            sh "docker push adreann/reactapp_prod:latest"
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
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@15.207.221.33 'cd /home/ubuntu && docker-compose pull && docker-compose up -d'"
                        } else if (env.GIT_BRANCH == "prod") {
                            sh "ssh -o StrictHostKeyChecking=no ubuntu@13.235.91.209 'cd /home/ubuntu && docker-compose pull && docker-compose up -d'"
                        }
                    }
                }
            }
        }

        stage('Deployment Status & Health') {
            steps {
                script {
                    sshagent(['trend-key']) {
                        def host = env.GIT_BRANCH == "dev" ? "15.207.221.33" : "13.235.91.209"
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${host} '
                        echo "==== Container Status ===="
                        docker ps --filter "name=reactapp_dev"
                        echo "==== Last 50 Logs ===="
                        docker logs --tail 50 reactapp_dev
                        if curl -s --head http://localhost:80/ | grep "200 OK" > /dev/null; then
                            echo "App is healthy"
                        else
                            echo "App is NOT healthy"
                        fi
                        '
                        """
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
