# Dockerized Web Server & CI/CD Deployment Setup

## Project Overview

This project demonstrates a basic DevOps deployment environment for a static website.

The application is built using HTML and CSS, served by Nginx, containerized using Docker, managed with Docker Compose, and validated through a GitHub Actions CI pipeline.

The project demonstrates:

- Linux
- Git and GitHub
- Docker
- Docker Compose
- Nginx
- Networking and Port Mapping
- Environment Variables
- Health Checks
- GitHub Actions CI/CD
- Basic Security
- Troubleshooting

---

## Architecture

```text
             Browser
                |
                v
        Host Port :8080
                |
                v
          Docker Container
                |
                v
             Nginx
                |
                v
          Static Website
```
## Request flow:
```
Browser
   |
localhost:8080
   |
Docker Port Mapping
8080:80
   |
Nginx Container
   |
Static Website Files
```
---

## Project Structure
```
devops-fresher-assignment/
│
├── .github/
│   └── workflows/
│       └── ci.yml
│
├── app/
│   ├── index.html
│   ├── style.css
│   └── 404.html
│
├── nginx/
│   └── nginx.conf
│
├── Dockerfile
├── docker-compose.yml
├── .dockerignore
├── .gitignore
├── .env.example
├── README.md
└── TROUBLESHOOTING.md
```
---

## Prerequisites
The following tools are required:

- Docker
- Docker Compose
- Git
- GitHub account
- Linux environment such as WSL or Ubuntu

Check Docker:
```
docker --version
docker compose version
```

Check Git:
```
git --version
```

---

## Setup Instructions

Clone the repository:
```
git clone https://github.com/Shipra-SG/devops-fresher-assignment.git
```

Move into the project directory:
```
cd devops-fresher-assignment
```

Create the environment file:
```
cp .env.example .env
```

Build and start the application:
```
docker compose up -d --build
```

Check the running container:
```
docker ps
```

Open the application:
```
http://localhost:8080
```

Stop the application:
```
docker compose down
```

---

## Docker

The application uses an Nginx Alpine-based Docker image.

The Dockerfile:

- Uses a lightweight Nginx image.
- Removes the default Nginx configuration.
- Copies the custom Nginx configuration.
- Copies static website files.
- Exposes port 80.
- Configures a Docker health check.
- Starts Nginx in the foreground.

Build the Docker image manually:
```
docker build -t devops-web-app:1.0 .
```

Run the image:
```
docker run -d -p 8080:80 --name devops-web-app devops-web-app:1.0
```

---

## Docker Compose

Docker Compose is used to simplify container deployment.

Start:
```
docker compose up -d
```

Stop:
```
docker compose down
```

Rebuild:
```
docker compose up -d --build
```

View running containers:
```
docker ps
```

View images:
```
docker images
```

View container logs:
```
docker logs devops-web-app
```

Execute commands inside the container:
```
docker exec -it devops-web-app sh
```

Stop a container:
```
docker stop devops-web-app
```

Start a container:
```
docker start devops-web-app
```

Restart a container:
```
docker restart devops-web-app
```

---

## Port Mapping

The application uses:
```
Host Port: 8080
Container Port: 80
```

Docker mapping:
```
localhost:8080 -> Container:80
```

Container Port:
The container port is the port on which the application runs inside the Docker container.

Nginx listens on:
```
80
```

Host Port:
The host port is the port on the local machine used to access the container.

The application is accessed using:
```
localhost:8080
```

Why Port Mapping Is Required:
Containers have their own isolated networking environment.
Port mapping allows traffic from the host machine to reach the application running inside the container.
If the host port is already being used, Docker cannot start the container with the same port mapping.

---

## Environment Variables

The project uses environment variables instead of hardcoding configuration.

Example:
```
APP_PORT=8080
```

The value is stored in:
```
.env
```

A template is provided:
```
.env.example
```

The .env file is excluded from Git using .gitignore.

Environment variables are useful because they allow configuration to change between environments without modifying application code.

---

## Nginx Configuration

Nginx is configured to:

Serve the static website.
Listen on port 80.
Return a custom 404 page.
Add basic security headers.
Enable gzip compression.
Cache static files.

### Security Headers

X-Content-Type-Options
```
nosniff
```
Prevents browsers from guessing the content type.

X-Frame-Options
```
DENY
```
Prevents the website from being loaded inside an iframe and helps reduce clickjacking attacks.

Referrer-Policy
```
strict-origin-when-cross-origin
```
Controls how much referrer information is sent with requests

---

## Health Check

Docker health checks are configured to verify that Nginx is responding.

Check health status:
```
docker ps
```

Example:
```
Up 30 seconds (healthy)
```

Detailed health information:
```
docker inspect devops-web-app
```

The health check verifies:
```
http://127.0.0.1/
```

If a container becomes unhealthy, Docker marks the container health status as unhealthy. The container may still be running, but monitoring systems or orchestration platforms can use this status to trigger alerts or recovery actions.

---

## GitHub Actions CI/CD

The project includes a GitHub Actions workflow:
```
.github/workflows/ci.yml
```
The workflow runs whenever code is pushed to the main branch.

Pipeline flow:
```
Git Push
   |
   v
GitHub
   |
   v
GitHub Actions
   |
   v
Checkout Repository
   |
   v
Validate Docker Compose
   |
   v
Build Docker Image
   |
   v
Verify Docker Image
   |
   v
Success / Failure
```

The pipeline performs:

- Repository checkout.
- Docker Compose validation.
- Docker image build.
- Docker image verification.

---

## Linux Commands Demonstrated

Current directory
```
pwd
```

List files
```
ls
```

Change directory
```
cd
```

Create directory
```
mkdir
```

Copy files
```
cp
```

Move files
```
mv
```

Remove files
```
rm
```

Display file contents
```
cat
```

Search text
```
grep
```

Change permissions
```
chmod
```

Process information
```
ps
```

Monitor processes
```
top
```

Disk usage
```
df -h
```

Memory usage
```
free -h
```

---

## Basic Security

The following security practices are used:

- Secrets and passwords are not committed to Git.
- .env is ignored using .gitignore.
- .env.example provides a safe configuration template.
- Only required ports are exposed.
- Nginx security headers are configured.
- A specific Docker image version is used instead of blindly using latest.
- A lightweight Alpine-based image is used.
- .dockerignore prevents unnecessary files from being included in the Docker build context.

Secrets should never be stored in GitHub repositories because repository history can preserve sensitive information even after files are deleted.

---

## Bonus 1: Docker Image Optimization

The Docker image was optimized using the following practices:

- Used `nginx:alpine` as a lightweight base image.
- Used `.dockerignore` to exclude unnecessary files from the Docker build context.
- Excluded Git files, environment files, logs, documentation files, and the local GitHub Actions runner directory.
- Copied only the required Nginx configuration and static website files into the image.
- Avoided installing unnecessary packages.

The image was inspected using:

```bash
docker images
docker history devops-web-app:latest-local
```

---

## Troubleshooting

Detailed troubleshooting scenarios are documented in:
```
TROUBLESHOOTING.md
```

The following scenarios are covered:

- Port already in use.
- Container keeps restarting.
-  Nginx returns 403 Forbidden.
- Docker image is very large.
- Website works inside the container but not in the browser.

---

## Screenshots

The following screenshots demonstrate the project:

-  Docker container running.
- Docker health check showing healthy.
- Website running at localhost:8080.
- Custom 404 page.
- Nginx security headers.
- GitHub Actions CI pipeline successful.

---

## Author
Shipra

---

## License
This project was created as part of a DevOps fresher assignment.
