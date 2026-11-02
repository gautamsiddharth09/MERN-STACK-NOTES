# Docker Notes for MERN Stack Developers
### Easy words • Interview preparation • Visual diagrams

> **Goal:** Understand Docker basics and explain them confidently in a MERN developer interview.

---

## 1. What is Docker?

**Docker** is a tool that packages an application and the things it needs into a **container**.

In easy words: Docker helps your app run in a similar way on your laptop, another developer's laptop, and a server.

### Why do developers use Docker?

- **Same environment:** Reduces “it works on my machine” problems.
- **Easy setup:** Team members can start services with fewer manual steps.
- **Isolation:** Apps can use different dependencies without mixing them.
- **Portability:** Containers can run on compatible machines and cloud servers.
- **Repeatable deployment:** Build and run the app using known instructions.

---

## 2. Visual: How Docker works

```text
       Your MERN application code
                  |
                  v
             Dockerfile
       (instructions to build)
                  |
                  v
             Docker image
          (app template)
                  |
             docker run
                  |
                  v
          Docker container
          (running app)
                  |
                  v
       Open the app through a port
```

**Remember:** Dockerfile = instructions, Image = template, Container = created instance.

---

## Images and Containers
Docker Image: What is it?
A Docker image is a read-only template or package used to create containers. It contains the application code and the files, libraries, and dependencies needed to run the app.
- Container ko create/run krne  ke lie container ko image ki need hoti hai, ek image ke kai sare container  run kar skte hain.
- Image is like a (os) and Image contain:-
- App
- Node
- Package Dependencies
- Dependencies
- Configs


For a Node.js / Express backend, an image may include:
- Node.js runtime
- Your backend code
- package.json and installed dependencies
- The default command to start the server
Think of an image as a recipe or app package. The image itself is not the running server.
Docker Container: What is it?
A Docker container is an instance created from an image. When you start a container, your application runs inside it.

Think of an image as a recipe and a container as the meal prepared from that recipe. You can create multiple containers from the same image.
Visual flow
Dockerfile (instructions)
        |
        | docker build
        v
Docker Image (template/package)
        |
        | docker run
        v
Docker Container (running app)

## 3. Important Docker words

| Word | Easy meaning | MERN example |
|---|---|---|
| Dockerfile | Instructions to build an image | Instructions for an Express API |
| Image | Template used to create a container | Node.js image with your backend |
| Container | An isolated app process created from an image | Running Express server |
| Docker Compose | Tool to run multiple services together | Backend + MongoDB |
| Registry / Docker Hub | Place to store and share images | Downloading the official Node image |
| Volume | Storage that can survive container removal | Keep MongoDB data |
| Network | Lets containers communicate | Backend talks to MongoDB |
| `.dockerignore` | Files Docker should skip in the build context | Skip `node_modules` and `.env` |

---

## 4. Image vs Container vs Virtual Machine

| Docker image | Docker container | Virtual machine |
|---|---|---|
| A template | An instance of an image | A virtual computer |
| Used to create containers | Runs an app process | Runs a guest operating system |
| Usually read-only layers | Has a writable container layer | Has its own guest OS and virtual resources |

**Interview answer:** An image is a template; a container is an instance created from that image. Containers share the host operating system's kernel, while a virtual machine runs its own guest OS.

---

## 5. Common Docker commands

A collection of commonly used Docker commands for building images, creating containers, managing Docker Hub, and working inside containers.

## 1. Build Docker Image

Build a Docker image from the Dockerfile in the current directory.

```bash
docker build -t test-image .
```

- `-t` → Assign a name/tag to the image.
- `.` → Current directory is the build context.

## 2. Create and Run Container

Create and run a container from an image.

```bash
docker run -it test-image
```

- `-i` → Keep STDIN open.
- `-t` → Allocate a terminal.

## 3. Run Container with Environment Variable

Pass an environment variable while running a container.

```bash
docker run -it -e PORT=5000 -p 5000:5000 test-image
```

- `-e PORT=5000` → Set environment variable.
- `-p 5000:5000` → Map host port 5000 to container port 5000.

## 4. Run Container Using `.env` File

Load environment variables from a `.env` file.

```bash
docker run -it --env-file .env test-image
```

## 5. Docker Hub Commands

### Login to Docker Hub

```bash
docker login
```

### Build Image with Docker Hub Username

```bash
docker build -t gautamsingh2104/test-image .
```

### Push Image to Docker Hub

```bash
docker push gautamsingh2104/test-image
```

### Pull and Run Image from Docker Hub

```bash
docker run -it -e PORT=8000 -p 8000:8000 gautamsingh2104/test-image
```

This command downloads the image if it is not available locally and runs a container.

## 6. Check Running Containers

```bash
docker ps
```

Shows only currently running containers.

## 7. Check All Containers

```bash
docker ps -a
```

Shows both running and stopped containers.

## 8. Check Running Container IDs

```bash
docker ps -q
```

Displays only the IDs of running containers.

## 9. Enter a Running Container

Open another terminal and execute:

```bash
docker exec -it container_name bash
```

- `exec` → Execute a command inside a running container.
- `-it` → Interactive terminal.
- `bash` → Open the Bash shell.

## 10. Navigate Inside Container

```bash
# Check current directory
pwd

# List files and folders
ls

# Enter App directory
cd App

# Go back one directory
cd ..

# Read index.js file
cat index.js

# Clear terminal
clear
```

## 11. View Container Logs

```bash
docker logs container_name
```

Displays logs generated by a container.

## 12. Stop Container

```bash
docker stop container_name
```

Stops a running container.

## 13. Start a Stopped Container

```bash
docker start container_name
```

Starts an existing stopped container.

## 14. Remove Container

```bash
docker rm container_name
```

Removes a stopped container.

## 15. Show Docker Images

```bash
docker images
```

Displays all locally available Docker images.

## 16. Remove Docker Image

```bash
docker rmi test-image
```

Removes a Docker image from your local system.

---

## Quick Revision Table

| Command | Purpose |
|---|---|
| `docker build -t test-image .` | Build an image |
| `docker run -it test-image` | Create and run a container |
| `docker run -e PORT=5000 ...` | Pass environment variables |
| `docker run --env-file .env ...` | Load variables from `.env` |
| `docker login` | Login to Docker Hub |
| `docker push image_name` | Push image to Docker Hub |
| `docker pull image_name` | Pull image from Docker Hub |
| `docker ps` | Show running containers |
| `docker ps -a` | Show all containers |
| `docker ps -q` | Show running container IDs |
| `docker stop container_name` | Stop a container |
| `docker start container_name` | Start a stopped container |
| `docker rm container_name` | Remove a container |
| `docker rmi image_name` | Remove an image |
| `docker logs container_name` | View container logs |
| `docker exec -it container_name bash` | Enter a running container |
| `docker images` | Show all images |

---



## 6. Create a Dockerfile for an Express backend

Example project:

```text
backend/
├── server.js
├── package.json
├── package-lock.json
└── Dockerfile
```

Create a file named `Dockerfile` inside `backend/`:

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

EXPOSE 5000

CMD ["npm", "start"]
```

Your `package.json` needs a start script, for example:

```json
{
  "scripts": {
    "start": "node server.js"
  }
}
```

### Dockerfile instructions in easy words

| Instruction | Meaning |
|---|---|
| `FROM` | Choose a base image |
| `WORKDIR` | Set the working folder inside the image |
| `COPY` | Copy files into the image |
| `RUN` | Run a command while building the image |
| `EXPOSE` | Document the port the app uses |
| `CMD` | Set the default command when the container starts |

### Build and run

Run from the `backend/` folder:

```bash
docker build -t mern-backend .
docker run --name mern-api -p 5000:5000 mern-backend
```

Check logs:

```bash
docker logs mern-api
```

### Important Express setting

The app must listen on an address reachable from outside the container, commonly `0.0.0.0`:

```js
app.listen(5000, "0.0.0.0", () => {
  console.log("Server running on port 5000");
});
```

---

## 7. What is `.dockerignore`?

Docker sends a **build context** (the files available to the build) when building an image. `.dockerignore` tells Docker which files to leave out.

Create `backend/.dockerignore`:

```text
node_modules
.env
.git
npm-debug.log
coverage
```

**Why?**
- Avoid copying your local `node_modules`.
- Keep `.env` out of the build context.
- Reduce unnecessary files and build time.

**Security tip:** Do not bake passwords, API keys, or JWT secrets into a Docker image. Supply secrets safely at runtime.

---

## 8. Docker Compose: Run backend + MongoDB

**Docker Compose** is a tool used to run multiple docker containers together using one file and one command.

- Main file docker ki

- docker-compose.yml


### Visual: MERN services

```text
          Browser
             |
             v
      React frontend
             |
          HTTP/API
             v
      Express backend
             |
        Docker network
             |
             v
          MongoDB
             |
             v
      Named data volume
      (keeps database data)
```

Example project:

```text
mern-app/
├── backend/
│   ├── Dockerfile
│   ├── server.js
│   └── package.json
└── compose.yaml
```

Create `compose.yaml` in the root folder:

```yaml
services:
  backend:
    build: ./backend
    env_file:
      - ./backend/.env
    ports:
      - "8000:5000"

  frontend:
    build: ./frontend
    ports:
      - "5173:5173"

  redis:
    image: redis
    ports:
      - "6379:6379"
```

This assumes the backend reads `process.env.MONGO_URI`, has a working Dockerfile, and connects to MongoDB.

### Useful Compose commands

```bash
docker compose up --build
```
Build images and start

```bash
docker compose down
```
Stop and remove containers and the default network.

```bash
docker compose down -v
```
Also removes Compose-managed named volumes. **Warning:** this can delete your stored MongoDB data.

### Why use `mongo` instead of `localhost`?

Inside a container, `localhost` means **that same container**.

Compose services on the same network can reach each other by service name. Therefore the backend uses:

```text
mongodb://mongo:27017/mern_app
```

`depends_on` controls startup order, but it does not by itself guarantee that MongoDB is ready. For a more reliable setup, add a health check and connection retries.

---

## 9. Docker volumes: Keep MongoDB data

## What are Docker Volumes?

Docker Volumes are used to store data permanently outside containers.

Data remains safe even if containers are deleted or restarted.

## Types of Docker Volumes

There are two common ways to persist data in Docker:

### 1. Bind Mount

A Bind Mount connects a folder from your local machine to a folder inside the container.

- **Data stored in:** Local Machine
- **Use case:** Development
- **Syntax:**

```bash
-v "local-folder:container-folder"
```

**Example:**

```bash
docker run -it -v "D:\project:/app" ubuntu
```

Here:
- `D:\project` is the folder on your local machine.
- `/app` is the folder inside the container.

Any changes made in the shared folder are reflected on both sides.

### 2. Named Volume

A Named Volume is managed by Docker and stores data in Docker's internal storage.

- **Data stored in:** Docker Internal Storage
- **Use case:** Database Storage
- **Syntax:**

```bash
-v volume-name:container-folder
```

**Example:**

```bash
docker run -it -v mongo-data:/data/db mongo
```

Here:
- `mongo-data` is the name of the Docker volume.
- `/data/db` is the directory inside the MongoDB container.

MongoDB data remains stored in the named volume even if the container is deleted.

## Difference Between Bind Mount and Named Volume

| Feature | Bind Mount | Named Volume |
|---|---|---|
| Storage Location | Local Machine | Docker Internal Storage |
| Managed By | User | Docker |
| Common Use Case | Development | Database Storage |
| Syntax | Local path : Container path | Volume name : Container path |

## Useful Docker Volume Commands

```bash
# List all volumes
docker volume ls

# Create a named volume
docker volume create mongo-data

# Inspect a volume
docker volume inspect mongo-data

# Remove a volume
docker volume rm mongo-data

# Remove all unused volumes
docker volume prune
```

**Important:** Removing a container does not automatically remove its named volume. However, deleting the volume itself can permanently delete its stored data.

## 10. Docker networking

Docker networking allows containers to communicate.

- Compose usually creates a network for your project automatically.
- Services on that network can communicate using service names.
- Containers do not need every port published to the host.
- Publish only the ports that need outside access.

Commands:

```bash
docker network ls
docker network inspect NETWORK_NAME
```

**Remember:** From the backend container, `mongo:27017` points to the MongoDB service in the Compose example. `localhost:27017` points back to the backend container itself.

---

## 11. Environment variables and secrets

A backend may need a MongoDB URL, JWT secret, or payment gateway keys.

Node.js example:

```js
mongoose.connect(process.env.MONGO_URI);
```

Compose example:

```yaml
services:
  backend:
    build: ./backend
    environment:
      MONGO_URI: mongodb://mongo:27017/mern_app
      JWT_SECRET: ${JWT_SECRET}
```

Set `JWT_SECRET` in your shell or a suitable secret-management system before running Compose.

**Do not commit secrets to Git or place them directly in a Dockerfile.** Keep local `.env` files out of Git and the Docker build context.

**Frontend warning:** React variables included in a browser bundle are visible to users. Never store private API keys or JWT signing secrets in frontend variables.

---

## 12. Dockerize a React frontend

For a production React app, one common approach is:
1. Use Node.js to build the frontend.
2. Use Nginx to serve the static output.

Example `frontend/Dockerfile` for a typical Vite project:

```dockerfile
FROM node:22-alpine AS build

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80
```

Build and run from `frontend/`:

```bash
docker build -t mern-frontend .
docker run -p 3000:80 mern-frontend
```

Open `http://localhost:3000`.

**Multi-stage build:** The first stage builds React. The second stage serves the generated files using Nginx. The production image does not need the full Node build environment.

**Note:** This example expects Vite output in `dist`. For React Router, configure Nginx fallback rules so refreshing nested routes works correctly.

---

## 13. Docker vs Kubernetes

| Docker | Kubernetes |
|---|---|
| Builds images and runs containers | Orchestrates containers across a cluster |
| Provides container tools and networking | Helps manage scaling, service discovery, and recovery |
| Compose is useful for local multi-service setups | Useful for larger distributed deployments |

**Interview answer:** Docker packages and runs applications in containers. Kubernetes manages containerized applications at scale. They can be used together.

---

## 14. Common interview questions

### Q1. What is Docker?
Docker is a platform for packaging and running applications in isolated containers.

### Q2. Image vs container?
An image is a template; a container is an instance created from it.

### Q3. What is a Dockerfile?
A text file with instructions to build a Docker image.

### Q4. What is Docker Compose?
A tool to define and run multi-container applications using YAML.

### Q5. What is Docker Hub?
A registry used to store and share container images.

### Q6. What is a volume?
Storage that can keep data even when a container is removed.

### Q7. What is port mapping?
It connects a host port to a container port, such as `-p 5000:5000`.

### Q8. `RUN` vs `CMD`?
`RUN` executes during image building. `CMD` sets the default command when the container starts.

### Q9. Does `EXPOSE` publish a port?
No. It documents the port. Use `-p` or Compose `ports` to publish it.

### Q10. What does `.dockerignore` do?
It leaves selected files out of the Docker build context.

### Q11. Why use `mongo` instead of `localhost` in Compose?
The service name `mongo` resolves to the MongoDB service on the Compose network. `localhost` inside the backend container refers to the backend container itself.

### Q12. How do you check container logs?
Use `docker logs CONTAINER_ID` or `docker compose logs SERVICE_NAME`.

### Q13. How does Docker help a MERN developer?
It makes app setup more consistent and helps run the backend, frontend, and database in repeatable environments.

---

## 16. Interview-ready answer

> I use Docker to package applications and keep their environments consistent across development and deployment. For a MERN project, I can create a Dockerfile for the Node.js and Express backend, use Docker Compose to run the backend and MongoDB together, and use a Docker volume to persist database data. I configure environment variables for application settings and port mapping to expose required services. Docker helps simplify setup and reduce environment-related problems.

---

## 17. Practice checklist

- [ ] Install Docker Desktop and check that Docker is running.
- [ ] Pull and run a Node.js image.
- [ ] Create a Dockerfile for Express.
- [ ] Build an image and map port 5000.
- [ ] Create a `.dockerignore` file.
- [ ] Run Express and MongoDB with Compose.
- [ ] Connect Mongoose using the MongoDB service name.
- [ ] Persist MongoDB data with a named volume.
- [ ] Read logs and troubleshoot a failed connection.
- [ ] Build a React frontend image using multiple stages.

---

## 18. Official learning links

- [Docker Get Started](https://docs.docker.com/get-started/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Compose documentation](https://docs.docker.com/compose/)
- [Official Node.js images](https://hub.docker.com/_/node)
- [Official MongoDB images](https://hub.docker.com/_/mongo)

**Study tip:** First understand image, container, Dockerfile, ports, volumes, and Compose. Then practise running Express + MongoDB together. Practical understanding is more valuable than memorizing every command.
