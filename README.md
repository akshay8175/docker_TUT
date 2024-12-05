# Docker Tutorial

# [Docker Tutorial](https://docs.docker.com/desktop/setup/install/linux/ubuntu/)

### Install Docker Desktop on Ubuntu

#### Table of contents
  1. Prerequisites
  2. Install Docker Desktop
  3. Launch Docker Desktop
  4. Upgrade Docker Desktop
  5. Next steps


### 1.  Prerequisites
  - To install Docker Desktop successfully, you must:
  - Meet the general [system requirements](https://docs.docker.com/desktop/setup/install/linux/#general-system-requirements)
  - Have an x86-64 system with Ubuntu 22.04, 24.04, or the latest non-LTS version.
  - For non-Gnome Desktop environments, `gnome-terminal` must be installed:
    > sudo apt install gnome-terminal

### 2. Install Docker Desktop

- Recommended approach to install Docker Desktop on Ubuntu:

    1. Set up Docker's package repository. See step one of Install using the [apt repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

    2. Download the latest [DEB package](https://desktop.docker.com/linux/main/amd64/docker-desktop-amd64.deb?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64&_gl=1*58o168*_ga*MTU4MjE5NDYzNy4xNzI0NjU1MDM1*_ga_XJWPQMJYHQ*MTczMzQwMjA1OS40LjEuMTczMzQwMjQzMS42MC4wLjA.). For checksums, see the [Release notes](https://docs.docker.com/desktop/release-notes/).

    3. Install the package with apt as follows:
       > sudo apt-get update
       
       > sudo apt-get install ./docker-desktop-amd64.deb

By default, Docker Desktop is installed at `/opt/docker-desktop`

### 3. Launch Docker Desktop

   - To start Docker Desktop for Linux:
        1. Open your Applications menu in Gnome/KDE Desktop and search for Docker Desktop.
        2. Select Docker Desktop to start Docker.
           The Docker Subscription Service Agreement displays.
        3. Select Accept to continue. Docker Desktop starts after you accept the terms.
           ######
   - Note that Docker Desktop won't run if you do not agree to the terms. You can choose to accept the terms at a later date by opening Docker Desktop.
   
   

   - Alternatively, open a terminal and run:
     > systemctl --user start docker-desktop

> docker compose version

`Docker Compose version v2.29.1`

> docker --version

`Docker version 27.1.1, build 6312585`

> docker version

```
Client: 
 Version:           23.0.5
 API version:       1.42
 Go version:        go1.21.12
<...>
```
- To enable Docker Desktop to start on sign in, from the Docker menu, select ***Settings > General > Start Docker Desktop*** when you sign in to your computer.

- Alternatively, open a terminal and run:
> systemctl --user enable docker-desktop

- To stop Docker Desktop, select the Docker menu icon to open the Docker menu and select ***Quit Docker Desktop***.

- Alternatively, open a terminal and run:
> systemctl --user stop docker-desktop

### 4. Upgrade Docker Desktop

Once a new version for Docker Desktop is released, the Docker UI shows a notification. You need to download the new package each time you want to upgrade Docker Desktop and run:

> sudo apt-get install ./docker-desktop-<arch>.deb

`Don't forget to substitute <arch> with the architecture you want.`

# [Overview of the Docker workshop](https://docs.docker.com/get-started/workshop/)

#### Table of contents
   1. What is a container?
   2. What is an image?

### What is a container?

A container is a sandboxed process running on a host machine that is isolated from all other processes running on that host machine. That isolation leverages kernel namespaces and cgroups, features that have been in Linux for a long time. Docker makes these capabilities approachable and easy to use. To summarize, a container:

 - Is a runnable instance of an image. You can create, start, stop, move, or  delete a container using the Docker API or CLI.
 - Can be run on local machines, virtual machines, or deployed to the cloud.
 - Is portable (and can be run on any OS).
 - Is isolated from other containers and runs its own software, binaries, configurations, etc.

If you're familiar with `chroot`, then think of a container as an extended version of `chroot`. The filesystem comes from the image. However, a container adds additional isolation not available when using `chroot`.

### What is an image?

A running container uses an isolated filesystem. This isolated filesystem is provided by an image, and the image must contain everything needed to run an application - all dependencies, configurations, scripts, binaries, etc. The image also contains other configurations for the container, such as environment variables, a default command to run, and other metadata.

# [Containerize an application](https://docs.docker.com/get-started/workshop/02_our_app/)

#### Table of contents
   1. Prerequisites
   2. Get the app
   3. Build the app's image
   4. Start an app container
   5. Summary

### 1. Prerequisites

  - You have installed the latest version of [Docker Desktop](https://docs.docker.com/get-started/get-docker/).
  - You have installed a [Git client](https://git-scm.com/downloads).
  - You have an IDE or a text editor to edit files. Docker recommends using [Visual Studio Code](https://code.visualstudio.com/).

### 2. Get the app

Before you can run the application, you need to get the application source code onto your machine.
  1. Clone the [getting-started-app repository](https://github.com/docker/getting-started-app/tree/main) using the following command:
     > git clone https://github.com/docker/getting-started-app.git

  2. View the contents of the cloned repository. You should see the following files and sub-directories.

```
├── getting-started-app/
│ ├── .dockerignore
│ ├── package.json
│ ├── README.md
│ ├── spec/
│ ├── src/
│ └── yarn.lock
```
### 3. Build the app's image

To build the image, you'll need to use a Dockerfile. A Dockerfile is simply a text-based file with no file extension that contains a script of instructions. Docker uses this script to build a container image.

1. In the `getting-started-app` directory, the same location as the `package.json` file, create a file named Dockerfile with the following contents:

```DockerFile
# syntax=docker/dockerfile:1

FROM node:lts-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
CMD ["node", "src/index.js"]
EXPOSE 3000
```

This Dockerfile starts off with a `node:lts-alpine` base image, a light-weight Linux image that comes with Node.js and the Yarn package manager pre-installed. It copies all of the source code into the image, installs the necessary dependencies, and starts the application.

2. Build the image using the following commands:

In the terminal, make sure you're in the `getting-started-app` directory. Replace `/path/to/getting-started-app` with the path to your getting-started-app directory.

> cd /path/to/getting-started-app

Build the image.
> docker build -t getting-started .

The `docker build` command uses the Dockerfile to build a new image. You might have noticed that Docker downloaded a lot of "layers". This is because you instructed the builder that you wanted to start from the `node:lts-alpine` image. But, since you didn't have that on your machine, Docker needed to download the image.

After Docker downloaded the image, the instructions from the Dockerfile copied in your application and used `yarn` to install your application's dependencies. The `CMD` directive specifies the default command to run when starting a container from this image.

Finally, the `-t` flag tags your image. Think of this as a human-readable name for the final image. Since you named the image `getting-started`, you can refer to that image when you run a container.

The `.` at the end of the `docker build` command tells Docker that it should look for the `Dockerfile` in the current directory.

### 4. Start an app container

Now that you have an image, you can run the application in a container using the `docker run` command.

1. Run your container using the `docker run` command and specify the name of the image you just created:

> docker run -d -p 127.0.0.1:3000:3000 getting-started

The `-d` flag (short for `--detach`) runs the container in the background. This means that Docker starts your container and returns you to the terminal prompt.

The `-p` flag (short for `--publish`) creates a port mapping between the host and the container. The `-p` flag takes a string value in the format of `HOST:CONTAINER`, where `HOST` is the address on the host, and `CONTAINER` is the port on the container. The command publishes the container's port 3000 to `127.0.0.1:3000` (`localhost:3000`) on the host. Without the port mapping, you wouldn't be able to access the application from the host.

2. After a few seconds, open your web browser to [http://localhost:3000](http://localhost:3000). You should see your app.


3. Add an item or two and see that it works as you expect. You can mark items as complete and remove them. Your frontend is successfully storing items in the backend.

At this point, you have a running todo list manager with a few items.

If you take a quick look at your containers, you should see at least one container running that's using the `getting-started` image and on port `3000`. To see your containers, you can use the CLI or Docker Desktop's graphical interface.


Run the `docker ps -a` command in a terminal to list your containers.

> docker ps -a

output:

|  CONTAINER ID  |   IMAGE           |        COMMAND              |    CREATED       |      STATUS       |          PORTS                 |           NAMES         | 
|----------------|-------------------|-----------------------------|------------------|-------------------|--------------------------------|-------------------------|
|  df784548666d  |  getting-started  |   "docker-entrypoint.sâ¦"  |  2 minutes ago   |    Up 2 minutes   |     127.0.0.1:3000 -> 3000/tcp |  priceless_mcclintock   |

