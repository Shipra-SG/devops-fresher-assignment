# Troubleshooting Guide

## Scenario 1: Port Already in Use

### Error

When running:
```bash
docker compose up -d
```
Docker may return:
```
bind: address already in use
```

### Why It Happens
This happens when the required host port is already being used by another application or container.

For this project, the host port is:
```
8080
```

### How to Identify the Process

Check which process is using the port:
```
sudo lsof -i :8080
```

Alternative:
```
ss -tulpn | grep 8080
```

Check running Docker containers:
```
docker ps
```

### How to Fix It

Stop the conflicting Docker container:
```
docker stop <container_id>
```

Or change the application port in the .env file:
```
APP_PORT=8081
```

Then restart the application:
```
docker compose up -d
```

---

## Scenario 2: Container Keeps Restarting

### Possible Causes

A container may continuously restart because of:

- Application failure
- Incorrect Docker command
- Invalid configuration
- Missing files
- Health check failures
- Incorrect restart configuration
- Investigation Commands

Check container status:
```
docker ps
```

Check logs:
```
docker logs <container_name>
```

Inspect container details:
```
docker inspect <container_name>
```

Check health status:
```
docker inspect --format='{{json .State.Health}}' <container_name>
```

### How to Troubleshoot
1- Check whether the main application process is failing.
2- Review container logs.
3- Verify the Dockerfile.
4- Check mounted files and configuration.
5- Check the health check command.
6- Confirm that the application process runs in the foreground.

For Nginx, the main process should remain active, for example:
```
CMD ["nginx", "-g", "daemon off;"]
```

---

## Scenario 3: Nginx Returns 403 Forbidden

### Possible Causes
A 403 error may happen because of:

- Incorrect file permissions
- Incorrect directory permissions
- Missing index file
- Incorrect Nginx root directory
- Invalid Nginx configuration

### How to Troubleshoot

Check Nginx logs:
```
docker logs devops-web-app
```

Enter the container:
```
docker exec -it devops-web-app sh
```

Check website files:
```
ls -la /usr/share/nginx/html
```

Check permissions:
```
ls -l /usr/share/nginx/html
```

Check the Nginx configuration:
```
nginx -t
```

Verify that:
```
index.html
```

exists in the configured Nginx root directory.

---

## Scenario 4: Docker Image Is Very Large

### How to Identify the Problem

Check Docker images:
```
docker images
```

Check image size and layers:
```
docker history <image_name>
```

### Why Docker Images Become Large

Common reasons include:
- Large base images
- Unnecessary files included in the build context
- Package caches
- Development dependencies
- Multiple unnecessary layers

### How .dockerignore Helps
.dockerignore prevents unnecessary files from being sent to the Docker build context.

Example:
```
.git
.env
*.log
```
This reduces build context size and helps prevent unnecessary or sensitive files from being included in the image.

### How to Reduce Image Size
- Use lightweight base images.
- Remove unnecessary files.
- Use .dockerignore.
- Combine related Dockerfile commands where appropriate.
- Remove package manager caches.
- Use multi-stage builds when needed.

This project uses:
```
nginx:1.27-alpine
```
which is lighter than many full operating-system-based images.

---

## Scenario 5: Website Works Inside Container but Not From Browser

## Investigation Steps

First check the container:
```
docker ps
```
Verify the port mapping.

Expected:
```
8080:80
```

Check whether the website works inside the container:
```
docker exec devops-web-app wget -O - http://127.0.0.1/
```

Check the application from the host:
```
curl -I http://localhost:8080
```

### Possible Causes
- Incorrect port mapping
- Wrong host port
- Nginx not listening on the expected container port
- Firewall restrictions
- Docker container not running
- Browser using an incorrect URL

### How to Fix

Verify the Docker Compose configuration:
```
ports:
  - "${APP_PORT}:80"
```

Verify the .env file:
```
APP_PORT=8080
```

Restart the application:
```
docker compose down
docker compose up -d --build
```

Check the container again:
```
docker ps
```

Then open:
```
http://localhost:8080
```

---

## General Troubleshooting Commands

Check running containers
```
docker ps
```

Check all containers
```
docker ps -a
```

Check logs
```
docker logs <container_name>
```

Follow logs
```
docker logs -f <container_name>
```

Inspect container
```
docker inspect <container_name>
```

Check processes inside container
```
docker exec <container_name> ps
```

Test Nginx configuration
```
docker exec <container_name> nginx -t
```

Restart container
```
docker restart <container_name>
```

Rebuild application
```
docker compose down
docker compose up -d --build
```
