Experiment 6: Comparison of Docker Run and Docker Compose

Name: Kashish Arora  Roll No: R2142230009  Course: Containerization and DevOps  Lab Experiment No: 6

Objective

To understand the relationship between docker run and Docker Compose, and to compare their configuration syntax and use cases by deploying single-container and multi-container applications.

PART A – Theory
1. Docker Run (Imperative Approach)
The docker run command creates and starts a container from an image. It requires explicit flags for port mapping (-p), volume mounting (-v), environment variables (-e), network configuration (--network), restart policies (--restart), resource limits (--memory, --cpus), and container name (--name).

Example:

```bash
docker run -d \
  --name my-nginx \
  -p 8080:80 \
  -v ./html:/usr/share/nginx/html \
  -e NGINX_HOST=localhost \
  nginx:alpine
