---
title: "Docker Commands I Use"
date: "2023-10-09"
description: "This a reference articel for the docker commands I use from time to time"
tags: [docker]
categories: [general]
---

## show the Docker version information
`docker version`
## list all images
 `docker images`
## download an image from registery
`docker pull <image name>`

## create and run a new ubuntu container and attach shell 
`docker run -it <image>  /bin/bash`

## run a container in detached mode
`docker run -d <image>`

## run a container with port mapp

`docker run -p 8080:8080 <image>`

## run a container with assigned name
`docker run --name <image>`
## exit container without terminating it
Press Ctrl-P-Ctrl-Q
## list all running containers
`docker ps`
## list all running and stopped containers
`docker ps -a`
## attach terminal to a running container
`docker exec -it <container name> /bin/bash`
## stop a container
`docker stop <name/id>` 
## delete a container
`docker delete <name/id>`
## remove all stopped containers
`docker container prune`
## create an image and tag it 
`docker build -t  <image:version>  .`
