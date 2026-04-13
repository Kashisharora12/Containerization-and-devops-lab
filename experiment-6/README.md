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


<img width="1217" height="248" alt="image" src="https://github.com/user-attachments/assets/ccd41673-318f-48c3-9628-9f7ad2bd54f9" />

Verify:

```bash docker compose ps ```

<img width="2020" height="150" alt="image" src="https://github.com/user-attachments/assets/fcdc79a9-dea2-450f-abfb-5edd19c1e44a" />

Stop containers:

```bash docker compose down ```

Command Explanation:Docker compose down: Stop and remove all services, networks (but preserves volumes)

Task 2: Multi-Container Application — WordPress + MySQL

Part A: Using Docker Run (Manual Method)

Step 1: Create network:

```bash
docker network create wp-net
```

Step 2: Run MySQL container: 

```bash
docker run -d \
  --name mysql \
  --network wp-net \
  -e MYSQL_ROOT_PASSWORD=secret \
  -e MYSQL_DATABASE=wordpress \
  mysql:5.7
```

<img width="1225" height="854" alt="image" src="https://github.com/user-attachments/assets/d55dffd4-2da3-4ca6-9906-92af462e7fb5" />

Step 3: Run WordPress container:

```bash
docker run -d \
  --name wordpress \
  --network wp-net \
  -p 8082:80 \
  -e WORDPRESS_DB_HOST=mysql \
  -e WORDPRESS_DB_PASSWORD=secret \
  wordpress:latest
```

<img width="1223" height="906" alt="image" src="https://github.com/user-attachments/assets/692a20de-d6db-4506-aab0-9c4eae1e70d8" />

Verify both containers running:

```bash docker ps ```

<img width="2020" height="150" alt="image" src="https://github.com/user-attachments/assets/c0704ac3-28bf-4cd7-a69e-50aa614ea694" />

Access in browser: ```bash http://localhost:8082 ```

📸 Screenshot – WordPress installation page (via Docker Run):

<img width="1226" height="729" alt="image" src="https://github.com/user-attachments/assets/9f18af90-df76-4c65-a008-dfef72de0f2a" />


<img width="942" height="610" alt="image" src="https://github.com/user-attachments/assets/05558d8d-5a59-47dc-a15b-e73729a3e900" />

Part B: Using Docker Compose (Structured Method)

Create ```bash docker-compose.yml: ```

```bash
version: '3.8'
services:
  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: wordpress
    volumes:
      - mysql_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    ports:
      - "8082:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_PASSWORD: secret
    depends_on:
      - mysql

volumes:
  mysql_data:
```

<img width="820" height="1196" alt="image" src="https://github.com/user-attachments/assets/0810f244-ea99-46d0-9783-3c43117b3aba" />

```bash docker compose up -d ```

Verify: 

```bash docker ps ```

Access in browser: ```bash http://localhost:8082 ```

📸 Screenshot – WordPress page via Docker Compose:

<img width="1216" height="674" alt="image" src="https://github.com/user-attachments/assets/1430909d-80db-41c8-8fc4-48cb7f559761" />

Stop and remove everything:

```bash docker compose down -v ```

🔄 PART C – Conversion & Build-Based Tasks

Task 3: Convert Docker Run to Docker Compose

Problem 1: Basic Web Application

Given Docker Run command:

```bash
docker run -d \
  --name webapp \
  -p 5000:5000 \
  -e APP_ENV=production \
  -e DEBUG=false \
  --restart unless-stopped \
  node:18-alpine
```

<img width="1226" height="270" alt="image" src="https://github.com/user-attachments/assets/3ed507b1-511c-46cd-a030-b51c8f0d90b4" />

Equivalent ```bash docker-compose.yml: ```

```bash
version: '3.8'
services:
  webapp:
    image: node:18-alpine
    container_name: webapp
    ports:
      - "5000:5000"
    environment:
      APP_ENV: production
      DEBUG: "false"
    restart: unless-stopped
```

<img width="1024" height="554" alt="image" src="https://github.com/user-attachments/assets/8d47691f-792c-4bfb-b66f-5f99ac95a551" />

Run:

```bash docker compose up -d ```

Verify: 

```bash docker compose ps ```

Problem 2: Volume + Network Configuration

Given Docker Run commands:

```bash
docker network create app-net

docker run -d \
  --name postgres-db \
  --network app-net \
  -e POSTGRES_USER=admin \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  postgres:15

docker run -d \
  --name backend \
  --network app-net \
  -p 8000:8000 \
  -e DB_HOST=postgres-db \
  -e DB_USER=admin \
  -e DB_PASS=secret \
  python:3.11-slim
```

Equivalent ```bash docker-compose.yml: ```

```bash
version: '3.8'
services:
  postgres-db:
    image: postgres:15
    container_name: postgres-db
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - app-net

  backend:
    image: python:3.11-slim
    container_name: backend
    ports:
      - "8000:8000"
    environment:
      DB_HOST: postgres-db
      DB_USER: admin
      DB_PASS: secret
    depends_on:
      - postgres-db
    networks:
      - app-net

volumes:
  pgdata:

networks:
  app-net:
```

<img width="1212" height="1232" alt="image" src="https://github.com/user-attachments/assets/67cae626-0a07-4bca-b38a-297c2268336e" />

Run:

```bash docker compose up -d ```

Stop and remove:

```bash docker compose down -v ```

📊 Comparison: Docker Run vs Docker Compose

<img width="668" height="411" alt="image" src="https://github.com/user-attachments/assets/8f476ddf-2860-427a-88d7-172ff0cf5e19" />

Result
Successfully completed:

Nginx container using Docker Run —   verified at ```bash http://localhost:8081 ```

Nginx container using Docker Compose —   verified at  ```bash http://localhost:8081 ``` 

WordPress + MySQL using Docker Run — verified at ```bash http://localhost:8082 ``` 

WordPress + MySQL using Docker Compose — verified at ```bash  http://localhost:8082 ``` 

Docker Run to Compose conversion (Problems 1 & 2)

Resource limits conversion (Task 4)

Custom Dockerfile with Compose (Task 5)

Multi-stage Dockerfile with Compose (Task 6)

Viva Questions
Q1. What is Docker Compose? Docker Compose is a tool used to define and run multi-container Docker applications using a YAML configuration file (docker-compose.yml).

Q2. What is the difference between Docker Run and Docker Compose? Docker Run executes containers manually using CLI commands (imperative), while Docker Compose manages multiple containers using a declarative YAML configuration file.

Q3. What does depends_on do? It ensures that one service starts before another. For example, WordPress waits for MySQL to start before launching.

Q4. Why are Docker networks used? Docker networks allow containers to communicate with each other using container names as hostnames, without exposing ports to the host machine.

Q5. What is the difference between image: and build: in Compose? image: pulls a prebuilt image from Docker Hub, while build: builds a custom image from a local Dockerfile.

Q6. What does docker compose down -v do? It stops and removes containers, networks, AND named volumes defined in the Compose file.


Conclusion

Docker Compose provides a structured and efficient way to manage multi-container applications compared to docker run. It simplifies deployment, improves maintainability, enables version control of infrastructure, and allows easy scaling of services using a single declarative YAML file.




























