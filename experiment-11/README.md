Experiment 11: Orchestration using Docker Compose & Docker Swarm

Name: Kashish Arora  Roll No: R2142230009  Course: Containerization and DevOps

Objective

Understand container orchestration by moving from Docker Compose to Docker Swarm. Learn how to deploy a stack, scale services, and observe self-healing in a Docker Swarm cluster using WordPress and MySQL setup.

Prerequisites

Docker installed with Swarm mode enabled

The docker-compose.yml file from Experiment 6 (WordPress + MySQL)

PART B – PRACTICAL (EXTENSION OF EXPERIMENT 6)

Task 1: Initialize Docker Swarm

Swarm mode turns your current machine into a manager node of a cluster.

```bash docker swarm init ```

Verify that your node is ready as a Swarm manager:

```bash docker node ls ```

Task 3: Deploy as a Stack (Not Just Compose)

In Swarm, we deploy a stack using the same Compose file. Swarm reads the file and creates services, which manage the containers automatically.

```bash docker stack deploy -c docker-compose.yml wpstack ```


