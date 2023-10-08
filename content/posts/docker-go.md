---
title: "Getting Started with Docker for Go"
date: "2023-09-21"
description: "my first post"
tags: [docker, go]
categories: [general]
---

## What Is a Container?

Containers are like isolating an application so it thinks it's running all by itself. It's similar to a virtual machine (VM) but uses the host's operating system instead of its own. To start a container, you begin with a container image that bundles up everything the application needs to run, making it easy to share and distribute.


The isolation that tricks a container into believing it has control over a whole Linux machine is achieved through the use of namespaces and control groups.

1. **Namespaces**: These are a way to create separate, isolated environments for various aspects of a container, such as its file system, network, and process space. Each namespace hides certain resources from the container, making it appear as if it has its own dedicated instance. For example, the PID (Process ID) namespace ensures that processes inside a container see only their own processes, unaware of others running on the host.

2. **Control Groups (cgroups)**: Control groups, often referred to as cgroups, are a mechanism for limiting and monitoring the resource usage of processes within a container. They allow you to control how much CPU, memory, disk I/O, and other resources a container can consume. This ensures that one container doesn't overwhelm the host system and allows for fair resource allocation among multiple containers.

Together, namespaces and control groups provide the foundation for the strong isolation and resource control that containers offer, making them an effective and efficient way to run applications in isolated environments on a shared host.


To truly understand containers and container images, hands-on experience is key. In this lesson, we'll guide you through practical examples to help you:

1. **Create a Container Image**: Learn how to build a container image that packages your Go code along with its static file dependencies. This process involves encapsulating your application and its required files into a self-contained image.

2. **Manage Environment Variables**: Discover how to pass environment variables into containers. These variables allow you to configure and customize your containerized application, adapting it to different runtime environments.

3. **Expose Ports**: Understand how to open ports within containers to enable external communication. This is essential for receiving requests or traffic from external sources into your containerized applications.

## Bundling Go Code into a Container Image


- In your terminal or command prompt, create a new directory for your project (if it doesn't already exist) and navigate to it.
- Initialize a Go module for your project using the command: `go mod init demo`
- Create a `main.go` file in the project directory.
- Open the `main.go` file with a text editor of your choice.
- Add the following sample code to the `main.go` file:


```go 
package main

import (
    "fmt"
    "net/http"
)

func main() {
    // Define a simple HTTP handler that responds with "Hello, Go!".
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, Go!")
    })

    // Start the web server on port 8080.
    fmt.Println("Server is running on :8080...")
    err := http.ListenAndServe(":8080", nil)
    if err != nil {
        fmt.Println(err)
    }
}
```

- Save the `main.go` file.

To view the web page served by your Go application, open your web browser and navigate to either of the following URLs:

- `http://0.0.0.0:8080`
- `http://localhost:8080`

After you enter one of these URLs in your browser's address bar and hit Enter, you should see the web page served by your Go application, which in this case will display "Hello, Go!"

To build your Go application into a standalone executable named "hello," you can use the `go build` command as you've mentioned. Open your terminal or command prompt, navigate to the directory where your `main.go` file is located (the "demo" directory in this case), and run the following command:

```bash
go build -o hello .
```

This command will compile your Go application and create an executable file named "hello" in the same directory. You can then run this executable to start your web server:

```bash
./hello
```

Once the server is running, you can access it in your web browser by navigating to `http://localhost:8080` or `http://0.0.0.0:8080`, as mentioned earlier.

## Creating a Dockerfile

Create a Dockerfile named "Dockerfile" in the same directory as your main.go file. Add the following content to the Dockerfile:

```docker
FROM scratch

EXPOSE 8080

COPY hello /

CMD ["/hello"]
```



Here are explanations for the lines in the Dockerfile:

```Dockerfile
FROM scratch
```

This line sets the base image for your Docker container. In this example, it starts with an empty base image called "scratch." "FROM scratch" means you're building the container from the ground up, and it doesn't include any pre-installed operating system or libraries. You'll need to add all the necessary files and dependencies explicitly.

```Dockerfile
EXPOSE 8080
```

The EXPOSE directive specifies that your container will listen on port 8080. It doesn't actually open the port; it serves as metadata to inform anyone running the container that port 8080 should be made available. You would still need to use the `-p` option when running the container to publish the port to the host: `docker run -p 8080:8080 my-hello-app`.

```Dockerfile
COPY hello /
```

This line copies the "hello" executable file from your host machine (the build context) into the root directory ("/") of the container. It ensures that the executable is available inside the container.

```Dockerfile
CMD ["/hello"]
```
The CMD directive specifies the default command to run when the container starts. In this case, it tells the container to execute the "/hello" binary. The use of square brackets is the JSON form of CMD, which doesn't invoke a shell. Since the base image is "scratch" and doesn't include a shell, this form is necessary to run the command directly without a shell.



To ensure compatibility with a Linux-based Docker container, use the following command to build your Go application as a Linux binary:
```bash
GOOS=linux go build -o hello .
```
This command sets the `GOOS` environment variable to "linux," instructing the Go compiler to target the Linux operating system when building the binary. It ensures that the resulting binary is suitable for running inside a Linux-based Docker container.
## Building the Container Image

Let's continue with building the Docker container image using the `docker image build` command with the `-t` flag to specify the image name and tag. This assumes that you're in the directory containing the necessary Dockerfile and application files. Here's the command:

```bash
docker image build -t hello:1 .
```

Here's a breakdown of the command:

- `docker image build`: This is the Docker command to build an image.
- `-t hello:1`: The `-t` flag is used to tag the image with a name and version. In this case, we're tagging the image as `hello` with version `1`.
- `.`: The period (`.`) at the end specifies the build context. It tells Docker to look in the current directory for the necessary files to build the image. Make sure you're in the directory containing your Dockerfile and any other files required for your application.

Running this command will start the image build process. Docker will read the instructions from the Dockerfile and create a new image tagged as `hello:1`. Once the build process is complete, you'll have your Docker image ready for use.




After running the `docker image build` command to create your Docker image, you can use the `docker image ls` command to list all the locally available Docker images. This will allow you to verify that your newly built image is listed among them.

Here's how you can use the `docker image ls` command:

```bash
docker image ls
```

When you run this command, you'll see a list of all Docker images available on your system. It will include information such as the repository, tag, image ID, creation date, and size.

Look for your `hello:1` image in the list. If the image was successfully built, it should be displayed along with its details.

Here's an example of what the output might look like:

```
REPOSITORY                          TAG       IMAGE ID       CREATED          SIZE
hello                               1         6195aa4aad82   35 seconds ago   6.78MB
```

In this example, you can see that the `hello:1` image has been successfully built and is listed with its image ID, creation date, and size. You can now use this image to run containers or push it to a container registry for further use. 


## 

To run the container and expose the specified ports, you can use the following command:

```bash
docker container run -P hello:1
```

Here's an explanation of the command and the subsequent steps:

1. `docker container run`: This command is used to start a new container from an image.
2. `-P`: The `-P` flag tells Docker to publish all exposed ports from the container to random ports on the host. This means that any ports defined as "EXPOSE" in your Dockerfile will be mapped to random ports on your host machine.

After running the above command, your container will start, and Docker will assign a random port on your host machine to the port 8080 (as specified in your Dockerfile). To check the running containers and view the port mappings, open another terminal window and use the `docker ps` command:

```bash
docker ps
```

This command will display a list of running containers, including information about their container IDs, images, commands, ports, and names.

For example:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
d5963ca57417        hello:1             "/hello"                 4 seconds ago       Up 2 seconds        0.0.0.0:32779->8080/tcp   reverent_archimedes
```

In this example, you can see that a container is running with the image `hello:1`. Docker has assigned a random host port (in this case, 32779) to the container's port 8080.

To access the web server running in the container, you can open a web browser and navigate to `0.0.0.0:<port>`, where `<port>` is the random host port assigned by Docker (e.g., `0.0.0.0:32779` in the example). This should allow you to see the web server in action.

Keep in mind that the assigned port on your host machine may vary each time you run the container, so be sure to use the correct port shown in the `PORTS` column when accessing the web server.


