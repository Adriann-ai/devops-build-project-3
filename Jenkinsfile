pipeline {
    agent any
    stages {
        stage (git checkout) {
           
        }
        stage ('docker build and tag') {
           ./buld.sh
        }
        stage ('docker push to hub') {
           ./deploy.sh
        }
        stage ()
    }
}