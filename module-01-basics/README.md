# Lab 01 - Docker: First Alpine Linux Containers

**Source:** [Play with Docker Training](https://training.play-with-docker.com/ops-s1-hello/?utm_source=gemini)

<!-- Language Switcher -->
<p align="right">
  <strong>English</strong> | <a href="./README.fr.md">Français 🇫🇷</a>
</p>

In this lab, you will explore the basics of how containers work, as well as how the Docker Engine runs and isolates containers from one another.

**Concepts covered:**

* Docker Engine


* Containers and images


* Image registries and Docker Hub


* Container isolation



---

## 1.0 Running Your First Container



To run your first Docker container, type:

```bash
docker container run hello-world

```

The output of the `hello-world` container tells you a bit more about what just happened. Essentially, the Docker Engine running in your terminal tried to find an image named `hello-world`. Since you just started, no image is stored locally (`Unable to find image...`), so the Docker Engine accesses its default Docker registry, which is **Docker Hub**, to search for an image named "hello-world".

It finds the image there, extracts it, and then runs it in a container. The only function of `hello-world` is to display the text you see in your terminal, and then the container closes.

![Docker first container](https://training.play-with-docker.com/images/ops-basics-hello-world.svg)



### Questions:



1. Do you think this is equivalent to running a virtual machine?


2. A container is an abstraction of:


* a. hardware


* b. application




3. Is it possible to use both virtual machines and containers in the same environment? How?



---

## 1.1 Docker Images



In the remainder of this lab, you will run an Alpine Linux container. Alpine is a lightweight Linux distribution, so it is fast to pull and run, making it a popular starting point for many other images.

To get started, run the following in your terminal:

```bash
docker image pull alpine

```

The `pull` command retrieves the `alpine` image from the Docker registry and saves it to our system. In this case, the registry is Docker Hub (you can change the registry).

You can use the `docker image` command to see the list of all images on your system. Run in your terminal:

```bash
docker image ls

```

### Running the Docker Container



Now run a Docker container based on this image. To do this, you will use the `docker container run` command:

```bash
docker container run alpine ls -l

```

When you call `run`, the Docker client finds the image (`alpine` in this case), creates the container, and then executes a command inside that container. When you ran `docker container run alpine ls -l`, you provided a command (`ls -l`), so Docker executed that command inside the container and displayed the directory listing. Once the `ls` command completed, the container stopped.

The fact that the container closed after executing our command is important.
![docker run details](https://training.play-with-docker.com/images/ops-basics-run-details.svg)
Test the following commands and observe what happens:

```bash
docker container run alpine echo "hello from alpine"

```

```bash
docker container run alpine /bin/sh

```

```bash
docker container run -it alpine /bin/sh

```

You ran each of your commands above in a separate container instance. We can see these instances using the `docker container ls` command, which lists running containers:

```bash
docker container ls

```

Since no container is running, you see an empty line. Let's try a more useful variant:

```bash
docker container ls -a

```

*What does the output of this command represent?*

To learn more about `run`, use `docker container run --help` to see a list of all the flags it supports.

---

## 1.2 Container Isolation



In the steps above, we ran several commands across container instances using `docker container run`. The `docker container ls -a` command showed us that there were multiple containers listed.

**Why are there so many containers listed if they all came from the alpine image?**

This is a critical security concept in the world of Docker containers! Even though each command used the same `alpine` image, each execution was a **separate and isolated container**. Each container has a distinct file system and runs in a different namespace; by default, a container has no way to interact with other containers, even those built from the same image.

Let's try another exercise to learn more about isolation:

```bash
docker container run -it alpine /bin/ash

```

`/bin/ash` is another type of shell available in the alpine image. Once the container is started and you are at the container command prompt, type the following commands:

```bash
echo "hello world" > hello.txt
ls

```

The first command creates a file called `hello.txt` containing the words "hello world". The second command gives you a directory listing and should display your new file. Now type `exit` to quit this container.

To show how isolation works, run the following command:

```bash
docker container run alpine ls

```

This is the same `ls` command we used in the container's interactive shell, but this time, did you notice that your `hello.txt` file is missing? **That's isolation.** Your command ran in a new, separate instance. The 2nd instance has no way to interact with the 1st instance because the Docker Engine keeps them separate.

Right now, the obvious question is: **"How can I get back to the container holding my hello.txt file?"**

Run again:

```bash
docker container ls -a

```

Graphically, here is what happened on our Docker Engine :
![Docker Container Isolation](https://training.play-with-docker.com/images/ops-basics-isolation.svg)

The container in which we created the `hello.txt` file is the same one in which we used the `/bin/ash` shell command (visible in the `COMMAND` column). The container ID number (e.g., `3030c9c91e12`) uniquely identifies this instance.

We can use a command to tell Docker to start this specific instance:

```bash
docker container start <container ID>

```

*(Tip: Instead of using the full ID, you can use just the first few characters, for example `3030`, provided they are unique in the list).*

Use the `docker container ls` command again to list running containers. The instance is active again. Since it is waiting for a command (via the ash shell), we can send it a command using `exec`:

```bash
docker container exec <container ID> ls

```

This time, the listing shows our `hello.txt` file because we used the exact instance in which we created that file.


![docker container exec](https://training.play-with-docker.com/images/ops-basics-exec.svg)

---

## Basic Terminology



* **Docker Daemon:** The background service running on the host that manages the creation, execution, and distribution of Docker containers.


* **Docker Client:** The command-line interface (CLI) tool that allows the user to interact with the Docker daemon.


* **Docker Hub:** A public registry of Docker images. You can think of it as a directory of all available Docker images ready to be downloaded and used.