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
```

<img width="1217" height="180" alt="image" src="https://github.com/user-attachments/assets/98d9867f-eb36-450b-8c9f-11f810d2b8ed" />

2. Docker Compose (Declarative Approach)
Docker Compose uses a YAML file (docker-compose.yml) to define services, networks, and volumes in a structured format. Instead of multiple commands, a single command is used: docker compose up -d

Equivalent Compose file:

```bash
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
```

<img width="874" height="602" alt="image" src="https://github.com/user-attachments/assets/b550340e-e5c3-452c-9c03-df89c48008a3" />

3. Mapping: Docker Run vs Docker Compose

   <img width="533" height="460" alt="image" src="https://github.com/user-attachments/assets/5ebb7447-5208-4a28-bd83-b05d0f330d8a" />


     4. Advantages of Docker Compose

1. Simplifies multi-container applications
2. Provides reproducibility
3. Version controllable configuration
4. Unified lifecycle management
5. Supports service scaling: docker compose up --scale web=3

   PART B – Practical Implementation

Task 1: Single Container Comparison

Step 1: Run Nginx Using Docker Run

Command:

```bash
docker run -d \
  --name lab-nginx \
  -p 8081:80 \
  -v $(pwd)/html:/usr/share/nginx/html \
  nginx:alpine
```

<img width="1217" height="181" alt="image" src="https://github.com/user-attachments/assets/df18b3a9-3fd3-4379-968c-1d83f845c146" />

Verify container is running:

```bash
docker ps
```

<img width="2020" height="150" alt="image" src="https://github.com/user-attachments/assets/2ec0d241-e572-4bc1-9e14-a98e77895f04" />

Access in browser: ```bash http://localhost:8081 ```

📸 Screenshot – Browser output (Docker Run):

![Browser output - Docker Run localhost:8081]

<img width="1600" height="152" alt="image" src="https://github.com/user-attachments/assets/7195a486-5bdf-4e49-b653-ef4d4a6dc208" />

Stop and remove container:

```bash
docker stop lab-nginx
docker rm lab-nginx
```

Step 2: Run Same Setup Using Docker Compose

Create : ```bash docker-compose.yml: ```

```bash
version: '3.8'
services:
  nginx:
    image: nginx:alpine
    container_name: lab-nginx
    ports:
      - "8081:80"
    volumes:
      - ./html:/usr/share/nginx/html
```

Run container:

```bash docker compose up -d ```







