# Docker container creation
1. Mount a directory. Structure of directory similar to linux system
2. Unshare a namespace. Creates a sandbox that app will run in to applying restrictions as to what the container can do. We can use `chroot` command to further restrict container to ensure it does not access files outside the container
3. Create control groups to limit the namespace. This allows us to limit the amount of CPU the container uses.
4. Set Linux capabilities on the chroot to restrict the set of system calls the namespace will make
5. Run the application!

# Container run times
- Responsible for loading container images from a repository, monitoring local system resources, isolating system resources for use of a container, and managing container lifecycle.
- Common container runtimes commonly work together with container orchestrators, such as Kubernetes.

## OCI runtimes
- Short for Open Container Initiative (OCI).
- Aims to standardize container technology like container images and runtimes.
- OCI runtime specification outlines what a container is and how they should be managed.
- Does not dictate how to do these things.
- Runtime spec is open source and actively maintained.
- `runc` from Docker is the de facto industry standard container runtime.
- `crun` is the Red Hat default container runtime and is written in C for performance.
- `youki` is a newer container runtime, written in Rust.

### Sandboxed OCI Runtimes
- gVisor and Balena containers use unikernels to restrict what containers can and cannot do.
- Kata Containers is a runtime for creating containers inside of VMs. Allows Docker benefits but additional security features are offered.

## CRI runtimes
- Container Runtime Interface (CRI).
- Made by K8s.
- CRI provides an API for running containers on container runtimes.

### CRI Runtimes
- `containerd` is a popular CRI runtime that uses `runc` to create OCI containers.
- `CRI-O` is a lightweight CRI runtime optimized for K8s, maintained by Red Hat, Intel, and others.

# Container Engines

- Container engines complement container runtimes and provide tools for managing containers efficiently.
- They offer various features, including automation, domain-specific languages, and more.
- Two popular container engines are Docker Engine and Podman.

## Docker Engine
- Docker Engine is the most widely used container engine.
- It includes:
  - Docker command-line client
  - Docker file syntax for creating images
  - An HTTP REST-based API for communication with container runtimes.
- By default, Docker Engine uses the `containerd` runtime, but this can be changed if needed.

## Podman
- Podman is Red Hat's container engine and is similar in functionality to Docker.
- Key differences:
  - Uses `Buildah` for creating container images instead of Dockerfile.
  - Default runtime is `crun` instead of `containerd`.
- Podman is the default container engine for Red Hat Enterprise Linux (RHEL) but can be adapted easily.

# Docker Files and Directories

- Docker is an essential tool for containerization, and understanding its files and directories is crucial for efficient usage.

## /var/lib/docker Directory
- Almost all of Docker's data is stored in the `/var/lib/docker` directory on the Linux VM.
- This includes containers, container images, and metadata used by the Docker client and runtime.

## /var/lib/docker/overlay Directory
- The `/var/lib/docker/overlay` directory is where container volumes reside.
- Container volumes store data from running containers

## Docker API Communication
- The Docker engine exposes an API for interaction with other components, such as the container runtime.
- Docker's command-line client communicates with this API over the network using a Unix socket.
- This Unix socket is a special file that facilitates communication between applications on the same machine.
- By default, the Unix socket is located at `/var/run/docker.sock`.

## /etc/docker/daemon.json Configuration
- Docker relies on `/etc/docker/daemon.json` for configuration and various properties like HTTP proxies and runtime settings.
- This configuration file may not exist when you first install Docker, but the Docker engine looks for it every time it runs.

Let's explore the critical files and directories:

- `/var/lib/docker`: This directory is home to almost all of Docker's data, including containers, container images, and metadata used by the Docker client and runtime.

- `/var/lib/docker/overlay`: Container volumes reside here, storing data from running containers (we'll cover this in detail later).

- Docker API Communication: The Docker engine exposes an API for interacting with components like the container runtime. Docker's command-line client communicates with this API over the network, using a Unix socket. The default socket location is `/var/run/docker.sock`.

- `/etc/docker/daemon.json`: Docker's configuration and properties, such as HTTP proxies and runtime settings, are defined in this file. It may not exist initially but is checked by the Docker engine each time it runs.

## Container Images
- Container runtimes use container images to create containers. These images are like "file systems" for containers packaged into single files.
- Container images are typically compressed tar files, often using gzip, to reduce their size.
- Each image consists of one or more "layers," which are compressed archives containing files that make up the container's filesystem.
- The final layer in an image typically contains instructions for configuring the container.
- Images also include a manifest file at their root, often in JSON format, providing information about layers and relationships.
- Manifests guide the container runtime in extracting layers and configuring containers.
- Containers are created from container images, and specifying an image name or path is necessary when creating a container.

# Using `tar` command

The following command is used to extract a file from a compressed archive using the `tar` utility:

```bash
tar -x -O -f hello-world.tar.gz manifest.json
```
- `tar`: This is the command-line utility for working with tarballs, which are collections of files bundled together, often used for compression and archiving purposes.

- `-x`: This option specifies the operation to be performed, which is "extract." It tells `tar` to extract files from the archive.

- `-O`: This option instructs `tar` to send the extracted content to the standard output (stdout) instead of creating files on disk. This is useful if you want to process or view the content without saving it to a file immediately.

- `-f hello-world.tar.gz`: This option, followed by an argument, specifies the archive file from which you want to extract files. In this case, `hello-world.tar.gz` is the name of the compressed archive.

- `manifest.json`: This is the name of the file you want to extract from the archive. It specifies the specific file within the archive that you want to extract.

### Explanation of the `tar` Command and Tarballs

- `tar`: This is the command-line utility for working with tarballs, which are collections of files bundled together. Tarballs are often used for compression and archiving purposes. Unlike ZIP files, which are more common on Windows systems, tarballs are widely used in Unix-like operating systems, such as Linux and macOS.

#### Tarballs vs. ZIP Files

- **Tarballs (`.tar`):**
  - Tarballs are archives that bundle multiple files and directories together.
  - They are commonly used in Unix-like operating systems for tasks like backup, software distribution, and system packaging.
  - Tarballs don't perform compression by default, which means they preserve the file permissions, ownership, and other metadata. This makes them suitable for system-level operations.
  - To compress a tarball, you usually combine it with compression utilities like `gzip` (`tar.gz`), `bzip2` (`tar.bz2`), or `xz` (`tar.xz`). This results in compressed tarballs with extensions like `.tar.gz`, `.tar.bz2`, or `.tar.xz`.

- **ZIP Files (`.zip`):**
  - ZIP files are archives that also bundle multiple files and directories together.
  - They are commonly used in Windows environments and are supported on various other operating systems as well.
  - ZIP files typically include compression, which reduces the size of the archived data, making them suitable for sharing files and folders.
  - ZIP archives are generally self-contained and include metadata, such as file permissions, but they may not always preserve Unix-specific attributes perfectly when extracted on non-Windows systems.

#### The `tar` Command Options:

- `-x`: This option specifies the operation to be performed, which is "extract." It tells `tar` to extract files from the archive.

- `-O`: This option instructs `tar` to send the extracted content to the standard output (stdout) instead of creating files on disk. This is useful if you want to process or view the content without saving it to a file immediately.

- `-f hello-world.tar.gz`: This option, followed by an argument, specifies the archive file from which you want to extract files. In this case, `hello-world.tar.gz` is the name of the compressed archive.

- `manifest.json`: This is the name of the file you want to extract from the archive. It specifies the specific file within the archive that you want to extract.

#### `jq` and the Pipe Operator (`|`):

- `jq`: This is a lightweight and powerful command-line tool for parsing and manipulating JSON data. It allows you to filter, transform, and query JSON documents easily.

- Pipe Operator (`|`): The pipe operator (`|`) is used in the command `tar -x -O -f hello-world.tar.gz manifest.json | jq .` to connect two commands together. It takes the output of the command on the left side (`tar`) and feeds it as input to the command on the right side (`jq`). In this case, it takes the extracted JSON data from `tar` and processes it using `jq`.

The entire command `tar -x -O -f hello-world.tar.gz manifest.json | jq .` is used to extract a file named `manifest.json` from the `hello-world.tar.gz` archive and then process the JSON data using `jq` to pretty-print it to the standard output.

Example output:
```bash
[
  {
    "Config": "d68fcc334f453a8c889c682226e6c6dc39694eaa4af54fdc8cc03bba03fdbb1c.json",
    "RepoTags": [
      // In style of image_name:version
      "test:latest"
    ],
    // Location of layers
    "Layers": [
      "7b473dec0fa9e1cd2ffeb04ca39b125972ca0927000ccd033404674671768b8a/layer.tar",
      "247460e92ef67fdf643394f29fdfdfcec2fde609010ec63e3b7bee779e1a4846/layer.tar"
    ]
  }
]
```
### Explanation of the JSON Output

The provided JSON output is an example of a JSON object. It represents information related to a Docker image. Let's break down the key components:

- **Config**: The `Config` field specifies the name of the configuration file for the Docker image. In this case, it is `"d68fcc334f453a8c889c682226e6c6dc39694eaa4af54fdc8cc03bba03fdbb1c.json"`.

- **RepoTags**: The `RepoTags` field is an array that contains the tags associated with the Docker image. In this example, there is a single tag: `"test:latest"`. Docker tags are used to label and identify different versions or variants of an image.

- **Layers**: The `Layers` field is an array that contains the paths to the layers that make up the Docker image. Docker images are typically composed of multiple layers, each representing a specific part of the image's file system. The paths in this example are:
  - `"7b473dec0fa9e1cd2ffeb04ca39b125972ca0927000ccd033404674671768b8a/layer.tar"`
  - `"247460e92ef67fdf643394f29fdfdfcec2fde609010ec63e3b7bee779e1a4846/layer.tar"`

These layers combine to create the complete filesystem for the Docker image, and they are stacked on top of each other to form the image's final state.

This JSON structure is commonly used for querying and managing Docker images and their associated information.

### Inspecting the image config

The provided JSON output represents the Docker image configuration extracted from a Docker image archive using the `tar` command and then processed using `jq`. When you create containers from docker images, they will inherit the same configuration. Let's break down the key components:

- **`architecture`**: This field specifies the architecture of the Docker image, which is `"arm64"` in this case. It indicates the CPU architecture for which the image is intended.  

- **`config`**: The `config` field contains detailed configuration information about the Docker image. It includes various settings such as environment variables, command to run when the container starts, image reference, volumes, and more.

  - **`Hostname`**, **`Domainname`**, **`User`**, and other fields: These fields specify various settings related to the container's configuration, such as hostname, domain name, user, and more.

  - **`Cmd`**: This field specifies the command that will be executed when the container is started. In this example, the command is `["/hello"]`.

  - **`Image`**: The `Image` field contains the image reference, typically in the form of a unique identifier (SHA-256 hash). It uniquely identifies the image.

- **`container_config`**: Similar to `config`, this field contains configuration information related to the container itself. It includes details about the command to run and other container-specific settings.

- **`created`**: This field specifies the creation timestamp of the Docker image. In this example, it is `"2022-11-29T21:16:36.165868669Z"`.

- **`docker_version`**: This field indicates the Docker version used to create the image. In this case, it is `"20.10.3"`.

- **`history`**: The `history` field provides a history of commands that were executed when building the Docker image. It shows the steps taken to create the image, including the commands used and timestamps.

- **`os`**: This field specifies the operating system used in the Docker image, which is `"linux"` in this case.  The docker container can only be run the same OS specified here.

- **`rootfs`**: The `rootfs` field describes the root filesystem of the container. It includes details about the type of filesystem and unique identifiers (SHA-256 hashes) for the filesystem layers.

- **`variant`**: This field indicates the variant of the architecture, such as `"v8"`.

This JSON structure is commonly used for inspecting Docker image configurations and provides insights into how the image is configured and built.

The entire command `tar -x -O -f hello-world.tar.gz d68fcc334f453a8c889c682226e6c6dc39694eaa4af54fdc8cc03bba03fdbb1c.json | jq .` is used to extract the `d68fcc334f453a8c889c682226e6c6dc39694eaa4af54fdc8cc03bba03fdbb1c.json` file from the `hello-world.tar.gz` archive and then process the JSON data using `jq` to pretty-print it to the standard output.

## Parent and child layers
Child layers store information about the parent image id, as well as the the order at which it was processed. You can inspect the manifest.json of the tar file to inspect this ordering. It's important to note that the layers are listed in reverse chronological order. Let's inspect one of the tar files:

```bash
[
  {
    "Config": "d68fcc334f453a8c889c682226e6c6dc39694eaa4af54fdc8cc03bba03fdbb1c.json",
    "RepoTags": [
      // In style of image_name:version
      "test:latest"
    ],
    // Location of layers
    "Layers": [
      "7b473dec0fa9e1cd2ffeb04ca39b125972ca0927000ccd033404674671768b8a/layer.tar",
      "247460e92ef67fdf643394f29fdfdfcec2fde609010ec63e3b7bee779e1a4846/layer.tar" # Let's use this one!
    ]
  }
]
```

Because we need to extract a tar file out of another tar file, we need to pipe the tar into another tar command and add a dash to the second option:
```bash
tar -x -O -f hello-world.tar.gz 247460e92ef67fdf643394f29fdfdfcec2fde609010ec63e3b7bee779e1a4846/layer.tar | tar -t -f - # The dash is here!
```

Output:
```bash
➜  03_02_before tar -x -O -f hello-world.tar.gz 247460e92ef67fdf643394f29fdfdfcec2fde609010ec63e3b7bee779e1a4846/layer.tar | tar -t -f -
hello-from-linkedin-learning # Good example of space efficient layer because only has necessary components - no linux directory structure
```
### Understanding Docker Image Layers

- Docker images are composed of multiple layers that contain snippets of files from a container's file system.

- When these layers are combined, containers created from the image inherit the files contained within them. For example, a Docker image with two layers may result in containers having two files in their root directory: `hello` and `hello-from-linkedin-learning`.

- If there are more layers with additional files, containers will include those files as well. This layering mechanism allows Docker images to be efficient and share common components among different images.

### Demystifying Docker Images

- Docker images are often viewed as mysterious collections of layers, but understanding how these layers come together clarifies their structure.

- Each layer adds or modifies files in the container's filesystem. These layers are stacked, with each layer building upon the previous one.

- The combination of layers, configuration, and metadata makes up the entirety of a Docker image.

### Container Runtimes and File Access

- Container runtimes play a crucial role in translating the layered structure of Docker images into usable containers.

- They merge the layers and present a unified filesystem to the container, providing a seamless experience for application execution.

- Containers see these files as if they were located in their own isolated filesystem, ensuring that applications run consistently regardless of the image's complexity.

