+++
authors = ["Mohamed Kamal"]
title = "Workflow: Deploying Node.js Apps with Kubernetes"
date = "2023-10-27"
description = "Explore the seamless deployment workflow for Node.js applications within Kubernetes clusters."
tags = [
    "Kubernetes",
    "Node.js",
    "Docker",
    "Deployment",
    "Containers",
]
categories = [
    "Application Deployment",
    "Kubernetes Workflow",
]
series = ["Kubernetes Deployment Workflow"]
+++

## Prerequisites


To successfully follow and execute the App Deployment Workflow within a Kubernetes environment, you'll need the following prerequisites:

### Software and Tools

1. **Node.js**: Ensure Node.js is installed on your local development machine to set up and manage the Node.js application.

2. **Docker**: Docker is essential for containerizing your application into a Docker image. Make sure Docker is installed and configured correctly to build and manage containerized applications.

3. **kubectl**: Install `kubectl`, the Kubernetes command-line tool, used for managing Kubernetes clusters. This tool will allow you to interact with your Kubernetes cluster, apply configurations, and manage deployments.

### Kubernetes Cluster

For setting up a Kubernetes cluster, the recommended option is using **kind (Kubernetes in Docker)**. "kind" is a tool for running Kubernetes clusters in containers. It provides a simple and efficient way to create a local Kubernetes environment for development or testing purposes.


## App Deployment Workflow


1. **Start with an App Code**: Begin with your application's source code, ready for containerization and deployment.

2. **Containerize the App into an Image with Docker**: Utilize Docker to encapsulate your application and its dependencies into a containerized image, ensuring consistency and portability across various environments.

3. **Store the Image in a Registry**: Upload the Docker image to a container registry such as Docker Hub or another private registry for storage and easy access.

4. **Define the Deployment in a Manifest File**: Create a Kubernetes manifest file specifying the deployment's configuration, such as Pods, Services, or Deployments.

5. **Post the Deployment**: Apply the Kubernetes manifest file to the cluster using `kubectl apply` to initiate the deployment process.


## Dockerizing a Node.js Web App

#### Create the Node.js app

First, set up the directory structure and files.

1. **Create a directory** for your Node.js web app and navigate into it.
2. **Create a `package.json`** file:
    ```json
    {
        "name": "docker_web_app",
        "version": "1.0.0",
        "description": "Node.js on Docker",
        "author": "First Last <first.last@example.com>",
        "main": "server.js",
        "scripts": {
        "start": "node server.js"
        },
        "dependencies": {
        "express": "^4.18.2"
        }
    }
    ```

3. **Create a `server.js`** file:
    ```javascript
    'use strict';
    const express = require('express');

    const PORT = 8080;
    const HOST = '0.0.0.0';

    const app = express();

    app.get('/', (req, res) => {
        res.send('Hello World');
    });

    app.listen(PORT, HOST, () => {
        console.log(`Running on http://${HOST}:${PORT}`);
    });
    ```

4. Run `npm install` to install the dependencies.

#### Create a Dockerfile

1. **Create a `Dockerfile`** in the app's root directory.

2. **Define the Dockerfile** content:
    ```docker
    FROM node:14

    WORKDIR /usr/src/app

    COPY package*.json ./
    RUN npm install

    COPY . .

    EXPOSE 8080

    CMD [ "node", "server.js" ]
    ```

3. **Create a `.dockerignore`** file in the same directory as your Dockerfile:
    ```bash
    node_modules
    npm-debug.log
    ```

#### Build Docker Image and Push to Registry

    Now, build and push the Docker image to a registry (e.g., Docker Hub):

1. **Build the Docker image** in the app directory:
    ```bash
    docker build -t your_username/docker_web_app:version .
    ```

2. **Run the Docker container** locally using the built image:
    ```bash
    docker run -p 8080:8080 your_username/docker_web_app
    ```

3. **Push the image to a Docker registry** (e.g., Docker Hub):
    ```bash
    docker push your_username/docker_web_app
    ```

    This Dockerized Node.js app is now packaged in a container and ready to be deployed or shared via a container registry. Adjust `your_username` to your Docker Hub username or the relevant registry.

    Make sure to replace "your_username" with your Docker Hub username or the relevant registry username when building and pushing the Docker image.

## Create a Pod Manifest (YAML)

1. **Create a new file** named `pod.yaml` in your project directory to define the Pod configuration.

2. **Define the Pod configuration** in the `pod.yaml` file. Here's a basic example:

   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: nodejs-app-pod
   spec:
     containers:
       - name: nodejs-app
         image: your_username/docker_web_app:version
         ports:
           - containerPort: 8080
   ```


## Deploying a Pod

**Apply the Pod manifest** to your Kubernetes cluster using `kubectl`:

   ```bash
   kubectl apply -f pod.yaml
   ```

   This command deploys the Pod to your Kubernetes cluster, and it will start running your Node.js app in a container.

#### Verify and Monitor the Pod

You can use `kubectl` to verify and monitor the state of your Pod:

- To check the status of your Pod:

   ```bash
   kubectl get pods
   ```

- To get more detailed information about the Pod:

   ```bash
   kubectl describe pod nodejs-app-pod
   ```

This completes the process of creating a Pod manifest in YAML format and deploying it to your Kubernetes cluster. Your Node.js web app is now running within a Kubernetes Pod. 


### Next Steps

1. **Service Object Creation**: Define and create a Service object within the Kubernetes environment. This object provides an abstraction to enable network access and discovery for your deployed application. It acts as a load balancer, exposing your application to other services in the cluster or external traffic.

2. **Configure ExtraPortMappings in Kind**: Adjust the Kind configuration to set up the ExtraPortMappings. This modification will facilitate traffic routing from the Kubernetes cluster to the host machine, ensuring seamless communication and accessibility to the application running within the cluster.

