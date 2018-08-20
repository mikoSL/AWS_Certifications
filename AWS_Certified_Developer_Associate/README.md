# aws_ecs (Elastic Container Service)

* All files are from AWS Cloud Developer Association (2018) course of Linuxacademy Ltd by Fernando Medina Corey. (5-start course!!)

![Website picture](../master/pic/penguin.PNG)

## Deploy a python flask application with AWS ECS
* EC2 instance as workstation to interact with Service and resource in AWS.
* Connect with AWS ECR (Elastic Container Register) and ECS.
* All application files are installed as container images to store in AWS ECR with Docker.
* ECS will interact with the images which is deployed in ECR.
* The container images will be sent through ECS upto another EC2 instance.
* Visit the public IP of the EC2 instance then the website application shows.

## Commands used for this project.
### Environment setup
```
sudo yum update -y
```

```
sudo yum install -y docker
```
```
sudo service docker start
```
```
sudo usermod -a -G docker $USER
```
```
docker info
```

### Get the files
```
git clone https://github.com/linuxacademy/cda-2018-flask-app.git
```
```
git checkout ecs-master
```
### Interact with docker
```
docker image ls
```
```
docker build -t cda-flask-app .
```
```
docker run -p 80:80 cda-flask-app
```
### Get the images to AWS ECR
```
aws ecr create-repository --repository-name cda-penguin-app
```
```
aws ecr get-login --region us-east-1 --no-include-email
```
* copy the output from the above command to sign in the repository.
* next associate the docker image with the repository.
```
docker tag cda-flask-app:latest 006105521810.dkr.ecr.us-east-1.amazonaws.com/cda-penguin-app
```
```
docker push 006105521810.dkr.ecr.us-east-1.amazonaws.com/cda-penguin-app
```
### Get started with AWS ECS with Fargate
* container definition
* task definition
* define service
* configure cluster
