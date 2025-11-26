This is a react application project and we need the below resourse
                    EC2 for Bastion and 2 other servers for dev and prod deployment
                    Git
                    Github
                    Docker
                    Dockerhub
                    Docker compose
                    Jenkins
                    Monitoring

Neceesary source code, docker file, Jenkins file, sh folders to build and push the images

Files from local machine pushed to Github repo "Dev branch"
Now Files will be available in github
Need to configure the webhook for auto trigger
            http://x.x.x.x:8080/github-webhook/

Make sure to create 2 new repository in docker hub
                  Dev repo - public
                  Prod repo - private

Configure Jenkins pipeline with Multibranch and it will automatically pull the files having Jenkins file
                    Jenkins file will have all necessary steps to exceute
                    Monitoring the container and URL monitoring also written in the jenkins file

 Docker compose need to install in the 2 servers for app deployment

 Neccesary permission for Jenkins and Ubuntu users added in Docker group  to build docker files by jenkins user and docker compose pull and up in remote server by ubuntu user
 
 So Source code pushed to Github-->pushed to Jenkins--> push the image to docker hub-->login to remote server and Docker compose cmd will be executed.
      
