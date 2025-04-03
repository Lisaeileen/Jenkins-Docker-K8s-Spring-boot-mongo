# Jenkins-Docker-K8s-Spring-Boot-Mongo

## Overview
This repository automates the CI/CD process for a **Spring Boot + MongoDB** application using **Jenkins, Docker, and Kubernetes**. The process follows a Jenkins **pipeline script** that builds, containers, pushes, and deploys the application seamlessly.

## Features
- **Git Integration:** Clones source code from GitHub
- **Maven Build:** Builds the project using Maven
- **Docker Containerization:** Packages the application into a Docker image
- **Docker Hub Push:** Pushes the image to a Docker registry
- **Kubernetes Deployment:** Deploys the container to a Kubernetes cluster

## CI/CD Pipeline Stages
The CI/CD process consists of the following stages:

### **1️⃣ Git Clone**
Jenkins pulls the source code from the repository.
- Repository URL: `https://github.com/lisaeileen/Jenkins-Docker-K8s-Spring-boot-mongo.git`

### **2️⃣ Maven Clean Build**
Jenkins installs Maven and builds the project.
- Uses `mvn clean package` to compile and package the application.

### **3️⃣ Docker Build**
Jenkins builds a Docker image for the application.
- Executes `docker build -t lisaeileen/spring-boot-mongo .`

### **4️⃣ Docker Push**
Jenkins authenticates to **Docker Hub** and pushes the built image.
- Uses `docker login` and `docker push lisaeileen/spring-boot-mongo`

### **5️⃣ Kubernetes Deployment**
Jenkins deploys the Docker container to Kubernetes using a **YAML configuration file**.
- Executes `kubectl apply -f springBootMongo.yml`

## Pipeline Script (Jenkinsfile)
Below is the **Groovy Script** used for the pipeline in Jenkins:

```groovy
node { 
    stage("Git Clone") {
        git branch: 'main', credentialsId: 'GITHUB_CRED', url: 'https://github.com/lisaeileen/Jenkins-Docker-K8s-Spring-boot-mongo.git'
    }

    stage("Maven Clean Build") {
        def mavenHome = tool name: "maven3.8.6", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"
    }

    stage("Docker Build Image") {
        sh "docker build -t lisaeileen/spring-boot-mongo ."
    }

    stage("Docker Push") {
        withCredentials([string(credentialsId: 'DOCKER_HUB_CRED', variable: 'DOCKER_HUB_CRED')]) {
            sh "docker login -u lisaeileen -p ${DOCKER_HUB_CRED}"
        }
        sh "docker push acadalearning/spring-boot-mongo"
    }

    stage("Kubernetes Deployment") {
        sh "kubectl apply -f springBootMongo.yml"
    }
}
```

## Prerequisites
Ensure the following are installed and configured:
- **Jenkins** (with Pipeline Plugin)
- **Maven** (`mvn`)
- **Docker** (`docker` and Docker Hub account)
- **Kubernetes CLI** (`kubectl`)
- **Kubernetes Cluster** (Minikube, AWS EKS, GKE, etc.)
- **GitHub Credentials in Jenkins**
- **Docker Hub Credentials in Jenkins**

## Running the Pipeline
1. **Set up Jenkins** and create a new **Pipeline Project**.
2. Use `https://github.com/lisaeileen/Jenkins-Docker-K8s-Spring-boot-mongo.git` as the **Repository URL**.
3. Add **GitHub and Docker Hub credentials** in Jenkins.
4. Configure the pipeline with the **Jenkinsfile**.
5. Click **Build Now** to start the pipeline.

## Deployment Verification
After deployment, check if the application is running on **Kubernetes**:
```sh
kubectl get pods
kubectl get services
```
To access the application:
```sh
kubectl port-forward svc/spring-boot-mongo 8080:8080
```
Then open: `http://localhost:8080`

## Conclusion
This project provides a **fully automated pipeline** for building, containerizing, and deploying a **Spring Boot + MongoDB** application using **Jenkins, Docker, and Kubernetes**.

