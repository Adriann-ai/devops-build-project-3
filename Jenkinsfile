pipeline {
    agent any
    stages {
        stage (git checkout) {
           
        }
        stage ('docker build and tag') {
           sh './buld.sh'
        }
        stage ('docker push to hub') {
           sh './deploy.sh'
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
