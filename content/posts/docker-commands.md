---
title: "Essential Docker Commands"
date: "2023-10-08"
description: "A comprehensive reference guide for frequently used Docker commands."
tags: ["docker"]
categories: ["technology"]
---

### Display Docker Version Information

```shell
docker version
```

This command provides information about the Docker Engine version.

### List All Docker Images

```shell
docker images
```

This command lists all the Docker images available on your system.

### Pull an Image from the Registry

```shell
docker pull <image_name>
```

You can use this command to download a Docker image from a container registry, like Docker Hub.

### Create and Run a New Ubuntu Container with Interactive Shell

```shell
docker run -it <image> /bin/bash
```

This command starts a new Docker container based on the specified image and opens an interactive shell (in this case, `/bin/bash`) inside the container.

### Run a Container in Detached Mode

```shell
docker run -d <image>
```

The `-d` flag runs a Docker container in detached mode, meaning it runs in the background.

### Run a Container with Port Mapping

```shell
docker run -p 8080:8080 <image>
```

This command maps port 8080 on the host to port 8080 inside the Docker container.

### Run a Container with an Assigned Name

```shell
docker run --name <container_name> <image>
```

You can specify a custom name for your Docker container using the `--name` flag.

### Exit a Container Without Terminating It

To exit a container without stopping it, press `Ctrl-P` followed by `Ctrl-Q` while in the container's terminal.

### List All Running Containers

```shell
docker ps
```

This command lists all the currently running Docker containers.

### List All Running and Stopped Containers

```shell
docker ps -a
```

By adding the `-a` flag, you can see all Docker containers, both running and stopped.

### Attach a Terminal to a Running Container

```shell
docker exec -it <container_name> /bin/bash
```

This command attaches your terminal to a running Docker container, allowing you to interact with it.

### Stop a Running Container

```shell
docker stop <container_name_or_id>
```

Use this command to stop a running Docker container by specifying either its name or ID.

### Remove a Container

```shell
docker rm <container_name_or_id>
```

This command removes a specified Docker container. You can use either the name or the container ID.

### Remove All Stopped Containers

```shell
docker container prune
```

This command removes all stopped Docker containers from your system.

### Build an Image and Tag It

```shell
docker build -t <image:version> .
```

This command builds a Docker image from a Dockerfile in the current directory and assigns it a custom tag.

--- 

These examples should help you understand how to use these essential Docker commands. Remember to replace `<image_name>`, `<image>`, `<container_name>`, and `<container_name_or_id>` with the actual names or IDs of your images and containers when using these commands.