# techsaloniki-workshop-2022

## an introduction to docker and kubernetes hands-on

In the past most companies used to develop big monolithic applications. Just in the last 10 years, an era of cloud computing and highly available microservices started to gain momentum. Container technology and Kubernetes made possible the rapid deployment and scaling of such applications. But what is this “Cloud native“ world? In this workshop we will make a short introduction to Docker containers and Container orchestration both in theory and in practice. We will containerize a simple Spring Boot application, deploy it to a Kubernetes cluster and see some of the amazing benefits of the Cloud, like self-healing applications, easy deployments, updates with zero downtime and super easy scale up/down activities.

## project requirements

1. Install Java SE JDK 11 x64: [official website](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) verify with `java -version`

2. Install Maven by following [this guide](https://mkyong.com/maven/how-to-install-maven-in-windows/) verify with `mvn -version`

3. Install Docker Desktop on Windows: [official documentation](https://docs.docker.com/desktop/windows/install/) verify with `docker run hello-world`

4. Download and start Minikube: [official website](https://minikube.sigs.k8s.io/docs/start/) verify with `minikube start`

5. Create a DockerHub account from [official website](https://hub.docker.com/) with 2 new repositories: *simple-webapp* and *java-spring-webapp*


## commands

#### 1. Create your first container image

```
$ mkdir myapp
$ echo "<html><body><h1>Hello World</h1><?php phpinfo(); ?></body></html>" >> myapp/index.php
$ docker build -t simple-webapp:latest .
$ docker run -d -p 80:80 -v ./myapp/ simple-webapp:latest
$ docker stop eaff
$ docker ps -a
$ docker image list
$ docker build -t ioannisgk/simple-webapp:latest .
$ docker image list
$ docker login
$ docker push ioannisgk/simple-webapp:latest

$ docker ps -a
$ docker system prune -a
$ docker image list

$ docker pull ioannisgk/simple-webapp:latest
$ docker image list
$ docker run -d -p 80:80 -v ./myapp/ ioannisgk/simple-webapp:latest
$ docker stop eaff
$ docker system prune -a
```

#### 2. Create and containerize a Spring boot application

```
$ mvn clean install
$ mvn spring-boot:run

$ docker build -t ioannisgk/java-spring-webapp:v1.0 .
$ docker run -d -p 80:8080 ioannisgk/java-spring-webapp:v1.0
$ docker stop eaff

# Update \src\main\java\com\spring\app\SpringAppApplication.java
$ mvn clean install
$ docker build -t ioannisgk/java-spring-webapp:v2.0 .
$ docker run -d -p 80:8080 ioannisgk/java-spring-webapp:v2.0
$ docker stop eaff

$ docker ps -a
$ docker image list
$ docker login
$ docker push ioannisgk/java-spring-webapp:v1.0
$ docker push ioannisgk/java-spring-webapp:v2.0
$ docker logout

###############################################

$ docker ps -a
$ docker system prune -a
$ docker image list

$ docker pull ioannisgk/java-spring-webapp:v1.0
$ docker pull ioannisgk/java-spring-webapp:v2.0
$ docker image list
$ docker run -d -p 80:8080 ioannisgk/java-spring-webapp:v1.0
$ docker stop eaff
$ docker run -d -p 80:8080 ioannisgk/java-spring-webapp:v2.0
$ docker stop eaff
$ docker system prune -a
```

#### 3. Deploy your Spring boot app to Kubernetes

```
$ minikube start
$ kubectl get nodes
$ kubectl create ns spring-webapp

# Deployment with 1 command
$ kubectl apply -f spring-webapp-deployment.yaml -n spring-webapp
$ kubectl get all -n spring-webapp
$ kubectl get pods -n spring-webapp
$ kubectl port-forward service/spring-webapp-service 80:8080 -n spring-webapp

# Self-healing pods
$ kubectl get pods -n spring-webapp --watch
$ kubectl delete pod spring-webapp-pod-01 -n spring-webapp # make sure to put the full pod name
$ kubectl get pods -n spring-webapp

# Zero downtime with rolling updates
$ kubectl get pods -n spring-webapp --watch
$ kubectl edit deploy spring-webapp-deployment -n spring-webapp # you can uodate the image tag
$ kubectl get pods -n spring-webapp
$ kubectl port-forward service/spring-webapp-service 80:8080 -n spring-webapp

# Easy scaling with 1 command
$ kubectl get pods -n spring-webapp --watch
$ kubectl scale deploy spring-webapp-deployment --replicas=8 -n spring-webapp
$ kubectl get pods -n spring-webapp
$ kubectl port-forward service/spring-webapp-service 80:8080 -n spring-webapp

$ kubectl delete ns spring-webapp
$ minikube stop
```
