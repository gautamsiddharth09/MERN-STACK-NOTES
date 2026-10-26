Docker Notes for MERN Stack Developers
Easy words • Interview preparation • Visual diagrams


1. What is Docker?
Docker is a tool that packages an application and the things it needs into a container.

In easy words: Docker helps your app run in a similar way on your laptop, another developer's laptop, and a server.
Why do developers use Docker?
- Same environment: Reduces “it works on my machine” problems.
- Easy setup: Team members can start services with fewer manual steps.
- Isolation: Apps can use different dependencies without mixing them.
- Portability: Containers can run on compatible machines and cloud servers.
- Repeatable deployment: Build and run the app using known instructions.
2. Visual: How Docker works
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
Remember: Dockerfile = instructions, Image = template, Container = created instance.
3. Important Docker words
Word	Easy meaning	MERN example
Dockerfile	Instructions to build an image	Instructions for an Express API
Image	Template used to create a container	Node.js image with your backend
Container	An isolated app process created from an image	Running Express server
Docker Compose	Tool to run multiple services together	Backend + MongoDB
Registry / Docker Hub	Place to store and share images	Downloading the official Node image
Volume	Storage that can survive container removal	Keep MongoDB data
Network	Lets containers communicate	Backend talks to MongoDB
.dockerignore	Files Docker should skip in the build context	Skip node_modules and .env


4. Image vs Container vs Virtual Machine
Docker image	Docker container	Virtual machine
A template	An instance of an image	A virtual computer
Used to create containers	Runs an app process	Runs a guest operating system
Usually read-only layers	Has a writable container layer	Has its own guest OS and virtual resources


Interview answer: An image is a template; a container is an instance created from that image. Containers share the host operating system's kernel, while a virtual machine runs its own guest OS.
5. Common Docker commands
Run these in your terminal. Replace example names or IDs when needed.
Command	What it does
docker --version	Shows Docker version
docker pull node:22-alpine	Downloads a Node image
docker images	Lists local images
docker ps	Shows running containers
docker ps -a	Shows all containers, including stopped ones
docker build -t mern-backend .	Builds an image from the current folder
docker run -p 5000:5000 mern-backend	Runs a container and maps the port
docker stop CONTAINER_ID	Stops a running container
docker start CONTAINER_ID	Starts a stopped container
docker restart CONTAINER_ID	Restarts a container
docker logs CONTAINER_ID	Shows container logs
docker exec -it CONTAINER_ID sh	Opens a shell inside a container, if available
docker rm CONTAINER_ID	Removes a stopped container
docker rmi IMAGE_ID	Removes an image that is not in use
docker stats	Shows live resource usage


Visual: Port mapping
Browser / Your computer             Docker container
http://localhost:5000  ----------->  Express app :5000

Command: docker run -p 5000:5000 mern-backend
                  HOST_PORT:CONTAINER_PORT
Example: -p 8080:5000 means you visit port 8080 on your computer, and Docker forwards traffic to port 5000 inside the container.

Important: EXPOSE 5000 in a Dockerfile documents the container port; it does not publish that port to your computer. Use -p or Compose ports to publish it.
6. Create a Dockerfile for an Express backend
Example project:
backend/
├── server.js
├── package.json
├── package-lock.json
└── Dockerfile
Create a file named Dockerfile inside backend/:
FROM node:22-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .

EXPOSE 5000

CMD ["npm", "start"]
Your package.json needs a start script, for example:
{
  "scripts": {
    "start": "node server.js"
  }
}
Dockerfile instructions in easy words
Instruction	Meaning
FROM	Choose a base image
WORKDIR	Set the working folder inside the image
COPY	Copy files into the image
RUN	Run a command while building the image
EXPOSE	Document the port the app uses
CMD	Set the default command when the container starts


Build and run
Run from the backend/ folder:
docker build -t mern-backend .
docker run --name mern-api -p 5000:5000 mern-backend
Check logs:
docker logs mern-api
Important Express setting
The app must listen on an address reachable from outside the container, commonly 0.0.0.0:
app.listen(5000, "0.0.0.0", () => {
  console.log("Server running on port 5000");
});
7. What is .dockerignore?
Docker sends a build context (the files available to the build) when building an image. .dockerignore tells Docker which files to leave out.

Create backend/.dockerignore:
node_modules
.env
.git
npm-debug.log
coverage
Why?
- Avoid copying your local node_modules.
- Keep .env out of the build context.
- Reduce unnecessary files and build time.
Security tip: Do not bake passwords, API keys, or JWT secrets into a Docker image. Supply secrets safely at runtime.
8. Docker Compose: Run backend + MongoDB
Docker Compose lets you define multiple services in one YAML file and start them together.
Visual: MERN services
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
Example project:
mern-app/
├── backend/
│   ├── Dockerfile
│   ├── server.js
│   └── package.json
└── compose.yaml
Create compose.yaml in the root folder:
services:
  backend:
    build: ./backend
    ports:
      - "5000:5000"
    environment:
      PORT: 5000
      MONGO_URI: mongodb://mongo:27017/mern_app
    depends_on:
      - mongo

  mongo:
    image: mongo:8
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
This assumes the backend reads process.env.MONGO_URI, has a working Dockerfile, and connects to MongoDB.
Useful Compose commands
docker compose up --build
Build images if needed and start services.
docker compose up -d --build
Start in the background.
docker compose ps
Show Compose services.
docker compose logs -f backend
Follow backend logs.
docker compose down
Stop and remove containers and the default network.
docker compose down -v
Also removes Compose-managed named volumes. Warning: this can delete your stored MongoDB data.
Why use mongo instead of localhost?
Inside a container, localhost means that same container.

Compose services on the same network can reach each other by service name. Therefore the backend uses:
mongodb://mongo:27017/mern_app
depends_on controls startup order, but it does not by itself guarantee that MongoDB is ready. For a more reliable setup, add a health check and connection retries.
9. Docker volumes: Keep MongoDB data
A volume stores data separately from a container's writable layer. The data can remain when the container is removed and recreated.
services:
  mongo:
    image: mongo:8
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
Named volume vs bind mount
Named volume	Bind mount
Managed by Docker	Uses a path on your computer
Good for database data	Useful for local code development
Example: mongo_data:/data/db	Example: ./backend:/app


Interview answer: I use a named volume to persist MongoDB data. I may use a bind mount during development so code changes are available inside the container.
10. Docker networking
Docker networking allows containers to communicate.
- Compose usually creates a network for your project automatically.
- Services on that network can communicate using service names.
- Containers do not need every port published to the host.
- Publish only the ports that need outside access.
Commands:
docker network ls
docker network inspect NETWORK_NAME
Remember: From the backend container, mongo:27017 points to the MongoDB service in the Compose example. localhost:27017 points back to the backend container itself.
11. Environment variables and secrets
A backend may need a MongoDB URL, JWT secret, or payment gateway keys.

Node.js example:
mongoose.connect(process.env.MONGO_URI);
Compose example:
services:
  backend:
    build: ./backend
    environment:
      MONGO_URI: mongodb://mongo:27017/mern_app
      JWT_SECRET: ${JWT_SECRET}
Set JWT_SECRET in your shell or a suitable secret-management system before running Compose.

Do not commit secrets to Git or place them directly in a Dockerfile. Keep local .env files out of Git and the Docker build context.

Frontend warning: React variables included in a browser bundle are visible to users. Never store private API keys or JWT signing secrets in frontend variables.
12. Dockerize a React frontend
For a production React app, one common approach is:
1. Use Node.js to build the frontend.
2. Use Nginx to serve the static output.
Example frontend/Dockerfile for a typical Vite project:
FROM node:22-alpine AS build

WORKDIR /app
COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80
Build and run from frontend/:
docker build -t mern-frontend .
docker run -p 3000:80 mern-frontend
Open http://localhost:3000.

Multi-stage build: The first stage builds React. The second stage serves the generated files using Nginx. The production image does not need the full Node build environment.

Note: This example expects Vite output in dist. For React Router, configure Nginx fallback rules so refreshing nested routes works correctly.
13. Docker vs Kubernetes
Docker	Kubernetes
Builds images and runs containers	Orchestrates containers across a cluster
Provides container tools and networking	Helps manage scaling, service discovery, and recovery
Compose is useful for local multi-service setups	Useful for larger distributed deployments


Interview answer: Docker packages and runs applications in containers. Kubernetes manages containerized applications at scale. They can be used together.
14. Common interview questions
Q1. What is Docker?
Docker is a platform for packaging and running applications in isolated containers.
Q2. Image vs container?
An image is a template; a container is an instance created from it.
Q3. What is a Dockerfile?
A text file with instructions to build a Docker image.
Q4. What is Docker Compose?
A tool to define and run multi-container applications using YAML.
Q5. What is Docker Hub?
A registry used to store and share container images.
Q6. What is a volume?
Storage that can keep data even when a container is removed.
Q7. What is port mapping?
It connects a host port to a container port, such as -p 5000:5000.
Q8. RUN vs CMD?
RUN executes during image building. CMD sets the default command when the container starts.
Q9. Does EXPOSE publish a port?
No. It documents the port. Use -p or Compose ports to publish it.
Q10. What does .dockerignore do?
It leaves selected files out of the Docker build context.
Q11. Why use mongo instead of localhost in Compose?
The service name mongo resolves to the MongoDB service on the Compose network. localhost inside the backend container refers to the backend container itself.
Q12. How do you check container logs?
Use docker logs CONTAINER_ID or docker compose logs SERVICE_NAME.
Q13. How does Docker help a MERN developer?
It makes app setup more consistent and helps run the backend, frontend, and database in repeatable environments.
15. Real-world troubleshooting
Problem: API is not accessible
1. Check docker ps.
2. Check port mapping with docker port CONTAINER_ID.
3. Confirm Express listens on 0.0.0.0.
4. Check docker logs CONTAINER_ID.
5. Check whether the host port is already in use.
Problem: Backend cannot connect to MongoDB
- Check MONGO_URI.
- Use the Compose service name, such as mongo, not localhost.
- Confirm MongoDB is ready and credentials/network settings are correct.
- Add connection retries or a health check when needed.
Problem: Code changes are not visible
Rebuild and recreate the service:
docker compose up --build -d
For development, use a bind mount and a development server if appropriate.
Problem: MongoDB data disappears
Check that MongoDB uses a named volume mounted at /data/db. Avoid docker compose down -v if you need to preserve that data.
Problem: Image is too large
- Add a .dockerignore.
- Use an appropriate base image.
- Use multi-stage builds for production frontend images.
- Copy package files before source files to improve build caching.
- Avoid including unnecessary development files.
16. Interview-ready answer
I use Docker to package applications and keep their environments consistent across development and deployment. For a MERN project, I can create a Dockerfile for the Node.js and Express backend, use Docker Compose to run the backend and MongoDB together, and use a Docker volume to persist database data. I configure environment variables for application settings and port mapping to expose required services. Docker helps simplify setup and reduce environment-related problems.

17. Practice checklist
- [ ] Install Docker Desktop and check that Docker is running.
- [ ] Pull and run a Node.js image.
- [ ] Create a Dockerfile for Express.
- [ ] Build an image and map port 5000.
- [ ] Create a .dockerignore file.
- [ ] Run Express and MongoDB with Compose.
- [ ] Connect Mongoose using the MongoDB service name.
- [ ] Persist MongoDB data with a named volume.
- [ ] Read logs and troubleshoot a failed connection.
- [ ] Build a React frontend image using multiple stages.
18. Official learning links
- Docker Get Started
- Dockerfile reference
- Docker Compose documentation
- Official Node.js images
- Official MongoDB images
Study tip: First understand image, container, Dockerfile, ports, volumes, and Compose. Then practise running Express + MongoDB together. Practical understanding is more valuable than memorizing every command.