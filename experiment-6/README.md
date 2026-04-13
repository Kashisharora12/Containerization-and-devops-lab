Experiment 6  : Comparison of Docker Run and Docker Compose

Name: Kashish Arora

Roll No: R2142230009  Course: Containerization and DevOps Lab Experiment No: 6

Objective

To understand the relationship between docker run and Docker Compose, and to compare their configuration syntax and use cases by deploying single-container and multi-container applications.

PART A – Theory
1. Docker Run (Imperative Approach)
The docker run command creates and starts a container from an image. It requires explicit flags for port mapping (-p), volume mounting (-v), environment variables (-e), network configuration (--network), restart policies (--restart), resource limits (--memory, --cpus), and container name (--name).

Example:

docker run -d \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html \
  -e NGINX_HOST=localhost \
  nginx:alpine
alt text

2. Docker Compose (Declarative Approach)
Docker Compose uses a YAML file (docker-compose.yml) to define services, networks, and volumes in a structured format. Instead of multiple commands, a single command is used: docker compose up -d

Equivalent Compose file:

version: '3.8'
services:
  nginx:
    image: nginx:alpine
    container_name: my-nginx
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      NGINX_HOST: localhost
    restart: unless-stopped

