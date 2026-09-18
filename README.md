# 🐳 Hands-On Docker Labs

Welcome to the **Docker Hands-On Labs** repository! This course is designed to take you from a complete beginner to confidently containerizing, networking, and optimizing multi-container applications for production.

---

## 🚀 Getting Started

You do not need to install Docker, WSL2, or any heavy software on your local machine to complete these labs. You can run everything entirely in your browser using **GitHub Codespaces**.

### Option A: Local Setup (Recommended)
Please prefer working locally, ensure you have the following installed:
* [Docker Desktop](https://docker.com) (Mac/Windows) or Docker Engine (Linux)
* [Git](https://git-scm.com)
* [VS Code](https://visualstudio.com)

### Option A: Open in GitHub Codespaces
If you have troubles running Docker locally, this would be an alternative option.
1. Click the **Code** button at the top right of this repository.
2. Select the **Codespaces** tab.
3. Click **Create codespace on main**.
4. Wait 1–2 minutes. You will get a fully configured Linux terminal with Docker already installed inside it!



---

## 📚 Course Syllabus & Modules

Each module lives in its own directory and contains a dedicated `README.md` with step-by-step instructions, a starter template, and a completed solution file.

| Module | Core Concepts Covered | Estimated Time | Link |
| :--- | :--- | :--- | :--- |
| **01: Docker CLI Basics** | Pulling images, running containers, mapping ports, and managing lifecycles. | 120 mins | [Go to Lab](./module-01-basics) |
<!-- | **02: Containerizing Apps** | Writing your first `Dockerfile`, building images, and managing layers. | 45 mins | [Go to Lab](./module-02-apps) |
| **03: Persistent Data** | Working with Docker Volumes and Bind Mounts to save application state. | 30 mins | [Go to Lab](./module-03-volumes) |
| **04: Multi-Container Apps**| Networking containers together using `docker-compose`. | 60 mins | [Go to Lab](./module-04-compose) |
| **05: Production Optimization**| Multi-stage builds, distroless images, and security best practices. | 45 mins | [Go to Lab](./module-05-optimization) |
-->
---

## 🛠️ Lab Workflow

To get the most out of these labs, follow this simple workflow inside each module folder:

1. **Read the Instructions:** Open the `README.md` in the specific module folder to see your objectives.
2. **Complete the Starter Code:** Open the file marked `.starter` (e.g., `Dockerfile.starter`) and fill in the missing blocks.
3. **Test Your Work:** Run the build or run commands specified in the lab instructions to see if your code works.
4. **Stuck? Check the Solution:** If you get blocked, compare your code with the `.solution` file in the same directory.

---

## 📋 Docker CLI Cheat Sheet

Here are the most common commands you will use throughout these labs:

### Managing Containers
* `docker run -d -p 8080:80 --name my-container nginx` — Run a container in the background (`-d`) with port forwarding (`-p`).
* `docker ps` — List running containers (`-a` to see all containers, including stopped ones).
* `docker stop <container_id>` — Safely stop a running container.
* `docker rm <container_id>` — Delete a stopped container.
* `docker logs -f <container_id>` — Follow live container logs.
* `docker exec -it <container_id> sh` — Open an interactive terminal inside a running container.

### Managing Images
* `docker build -t my-app:1.0 .` — Build an image from a Dockerfile in the current directory.
* `docker images` — List all locally stored images.
* `docker rmi <image_id>` — Delete a local image.

### System Cleanup
* `docker system prune -a --volumes` — **The Panic Button.** Cleans up all stopped containers, unused networks, dangling images, and build caches to free up disk space.

---

## ⚖️ License
This project is open-source and available under the [MIT License](LICENSE).
