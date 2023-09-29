# Quick notes on docker
## Container vs VMs
![Container vs vms](image.png)
 
Containers
- Aimed to virtualise/emulate an OS
- Run on container run times (I.e. Docker, podman)
-	Work with VMs to allocate hardware, copy files and directories to run app
-	Do not need to emulate hardware or need to be booted up
-	Do not require any OS installation
-	Apps can start very quickly
-	Take less space as we don’t need to run an OS
-	Can only run one app at a time
-	Can interact with their host 

**To clarify**:
1.	Shared Kernel: Containers are designed to be lightweight by sharing the host machine's operating system kernel. The kernel is the core part of the operating system that manages system resources and hardware. Containers use the same kernel as the host, so they don't need to replicate all the functions of an entire operating system.
2.	Container Runtimes: Container runtimes like Docker or containerd are responsible for managing containers. They package the application code, libraries, and dependencies along with a minimal runtime environment, which includes just enough of the operating system to run the application. This runtime environment is what allows containers to execute code without a full OS. This bundled environment is what we refer to as the "container."

3.	Namespace and Control Groups: Linux provides features like namespaces and control groups (cgroups) that allow containers to create isolated environments for processes within the same host OS. Namespaces provide process isolation, and cgroups control resource allocation (CPU, memory, etc.). These features enable multiple containers to run on the same host without interfering with each other.

So, while containers do rely on an operating system, they do not require a separate, full-fledged OS installation for each container. Instead, they use the host OS's kernel and create isolated runtime environments managed by container runtimes. This approach makes containers highly efficient and allows them to start quickly, as they only need to set up the environment necessary for the specific application they contain. Containers share the host operating system's kernel, and container runtimes like Docker facilitate the creation and management of these isolated environments. Docker, along with other container runtimes, provides the necessary tools and mechanisms to bridge the gap between the host OS and the container.

## VMs
-	Has a hypervisor which emulates real hardware – bridge VMs to hosts
-   Takes up a lot of space
-	Requires you to install/configure the OS
-	Can run multiple apps at the same time
-	Cannot interact with their hosts

## Container structure
![Container structure](image-1.png)
 
## Docker commands
-	`Docker run` is short for `docker container create` + `docker container start`  + `docker container attach`
-	`Docker ps` to view active containers, with -a to view all containers
Docker steps
-	Each step in a docker image is an instruction. Depending on the task, it may or may not create an image. These images are called intermediate images. The intermediate images “capture” any changes performed. While each step may not create a new image, it does create a new layer. These layers are compressed to make the final docker image.
-	Docker images act as templates to help spin up docker containers and are often starting points when using docker
-	To achieve this, we use a build command to build docker images and docker run to create containers. If you want to build and run your images, you should build first then run it after.

## Docker servers
-	Dockerfiles that do not immediately exit after running the entrypoint command are called docker servers
-	Building and run docker server files will continue running indefinitely, but we can stop it using `docker kill [container-id]`
-	We can also use docker exec to execute commands inside the container, which is useful for debugging. For example: `docker exec --interactive --tty 32ef748596bd bash`

The above tells docker “we want to execute (exec) commands and allow us to interact (interactive) with the container using our terminal (tty) to this id 32ef748596bd using bash script (bash).

Docker does not stop containers and remove them for you – it’s the dev’s responsibility. You can run `docker stop id` to stop a particular docker container. You can also force the container to stop using `docker stop -t 0 id` but it can lead to data loss, so use sparingly. 

You can run `docker rm` to remove docker containers that are stopped, which helps free up battery usage and your computer’s resources. You can also use the -f flag in docker rm to stop and remove a docker container. You can even remove all docker containers in one go using  `docker ps -aq | xargs docker rm`

Docker ps -aq shows all docker container ids, the takes the output, feeding it to xargs. Xargs is a useful as it allows us to perform actions on a list, similar to a loop. Here we run docker rm to apply the command to each member of docker ps -aq.

