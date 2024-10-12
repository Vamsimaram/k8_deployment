### Minikube Project 

## Project Overview

This project demonstrates how to set up a Kubernetes cluster using **Minikube** on an **Amazon Linux 2 EC2 instance**. The cluster will be used to deploy Java-based microservices, which are packaged into Docker containers. The project provides a complete guide to installing necessary tools such as **Docker**, **Maven**, **Java**, and **Git**, followed by building and deploying Docker images into Kubernetes.

It focuses on the core concepts of **Kubernetes**, **Docker**, and **Java microservices**, enabling a full end-to-end deployment pipeline for a microservice architecture.

## Project SetUp
This document provides a step-by-step guide to setting up Minikube, Docker, Maven, Git, Java on an Amazon Linux 2 EC2 instance, and deploying a Java-based application using Docker and Kubernetes.


## Setup Instructions

## Step 1: Install Minikube and Docker on Amazon Linux 2 EC2

## Update the instance
yum update -y

## Install Docker
amazon-linux-extras install docker

yum install docker -y

## Start Docker and enable it to run on boot
systemctl start docker

systemctl enable docker

## Install conntrack and Minikube
yum install conntrack -y

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

## Start Minikube with Docker driver
/usr/local/bin/minikube start --force --driver=docker


## Step 2: Install Maven, Git, and Java

## Install Maven
cd /opt/

wget https://dlcdn.apache.org/maven/maven-3/3.9.4/binaries/apache-maven-3.9.4-bin.tar.gz

tar xvzf apache-maven-3.9.4-bin.tar.gz

## Set up Maven environment variables
vi /etc/profile.d/maven.sh
## Add the following two lines:
export MAVEN_HOME=/opt/apache-maven-3.9.4

export PATH=$PATH:$MAVEN_HOME/bin
## Save and exit
source /etc/profile.d/maven.sh

## Install Git
yum install git -y

## Install Java
yum install java -y

## Step 3: Install kubectl

## Install kubectl
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.20.4/2021-04-12/bin/linux/amd64/kubectl

chmod +x ./kubectl

mkdir -p $HOME/bin

cp ./kubectl $HOME/bin/kubectl

export PATH=$HOME/bin:$PATH

echo 'export PATH=$HOME/bin:$PATH' >> ~/.bashrc

source $HOME/.bashrc

## Verify kubectl installation
kubectl version --short --client

## Step 4: Clone the Project Repository

## Clone the GitHub repository for the project
git clone https://github.com/Vamsimaram/k8_deployment.git

## Step 5: Build and Push Docker Images for Java Services

Build and push the images for 3 Java services (shopfront, productcatalogue, stockmanager)

## Service 1 - Shopfront
cd kubernetes/shopfront/

mvn clean install -DskipTests

docker build -t vmaram9/shopfront:latest .

docker login

docker push vmaram9/shopfront:latest

## Service 2 - Product Catalogue
cd ../productcatalogue/

mvn clean install -DskipTests

docker build -t vmaram9/productcatalogue:latest .

docker push vmaram9/productcatalogue:latest

## Service 3 - Stock Manager
cd ../stockmanager/

mvn clean install -DskipTests

docker build -t vmaram9/stockmanager:latest .

docker push vmaram9/stockmanager:latest

## Step 6: Deploy Services to Kubernetes

## Deploy the services using kubectl
cd ../kubernetes/

kubectl apply -f shopfront-service.yaml

kubectl apply -f productcatalogue-service.yaml

kubectl apply -f stockmanager-service.yaml

## Step 7: Verify the Pods

## Check if all pods are running
kubectl get pods

## Step 8: Start Kubernetes Dashboard

## Start the Minikube Kubernetes dashboard
/usr/local/bin/minikube dashboard

## Step 9: Set Up Proxy and Open Dashboard

## Open a new EC2 terminal window and set up the proxy
kubectl proxy --address='0.0.0.0' --accept-hosts='^*$'
## Ensure EC2 security group allows traffic from all IPs and all traffic

## Open the Kubernetes dashboard in the browser
http://<EC2-IP>:8001/api/v1/namespaces/kubernetes-dashboard/services/http:kubernetes-dashboard:/proxy/#/pod?namespace=default

## Step 10: Port Forward the Services

## Forward the services to different ports on EC2

## For Shopfront
kubectl port-forward --address 0.0.0.0 svc/shopfront 8080:8010

## For Product Catalogue
kubectl port-forward --address 0.0.0.0 svc/productcatalogue 8090:8020

## For Stock Manager
kubectl port-forward --address 0.0.0.0 svc/stockmanager 9008:8030

## Step 11: Access the Applications

Access the applications via browser

## Product Catalogue:
http://<EC2-IP>:8090/product

## Stock Manager:
http://<EC2-IP>:9008/stocks

## Step 12: Analyze the Kubernetes Dashboard
# Explore the Kubernetes dashboard to view pods, services, and deployments.


