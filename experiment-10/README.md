Experiment 10: SonarQube — Static Code Analysis

Name: Kashish Arora  Roll No: R2142230009  Course: Containerization and DevOps

Objective

Perform static code analysis on a Java application using SonarQube to automatically detect bugs, security vulnerabilities, and code smells — and understand how it integrates into a CI/CD pipeline.

Theory

Problem Statement

Code bugs and security issues are often found too late — during testing or even after deployment. Manual code reviews are slow, inconsistent, and don't scale 

as teams grow.

What is SonarQube?

SonarQube is an open-source platform that automatically scans source code for bugs, security vulnerabilities, and maintainability issues — without running the 

code. This is called static analysis.

Key Terms

<img width="708" height="367" alt="image" src="https://github.com/user-attachments/assets/11377b04-70b9-42b9-98ab-284e54587d59" />

Lab Architecture

```bash
┌─────────────────────────────────────────────────────────┐
│                    Your Machine / CI                    │
│                                                         │
│   ┌──────────────┐        ┌──────────────────────────┐  │
│   │  Your Code   │──────▶ │    Sonar Scanner         │  │
│   │  (Java, JS,  │ scans  │  (CLI / Maven / Jenkins) │  │
│   │   Python...) │        └────────────┬─────────────┘  │
│   └──────────────┘                     │ sends report   │
│                                        ▼                │
│                          ┌─────────────────────────┐    │
│                          │   SonarQube Server      │    │
│                          │   (runs on port 9000)   │    │
│                          │   ┌─────────────────┐   │    │
│                          │   │ Analysis Engine │   │    │
│                          │   │ Quality Gates   │   │    │
│                          │   │ Web Dashboard   │   │    │
│                          │   └────────┬────────┘   │    │
│                          └───────────┼─────────────┘    │
│                                      │ stores results   │
│                          ┌───────────▼─────────────┐    │
│                          │   PostgreSQL Database   │    │
│                          └─────────────────────────┘    │
└─────────────────────────────────────────────────────────┘
```

Prerequisites

Docker Desktop installed and running

Maven (mvn) installed (or use the Docker-based scanner)

Terminal / command line

Check Docker is running:

```bash
docker --version
docker-compose --version
```

Step 1: Start the SonarQube Server

Navigate to the experiment folder and start all containers:

```bash
cd ~/Desktop/exp10
docker-compose up -d
```

Watch logs until you see "SonarQube is operational":

```bash docker-compose logs -f sonarqube ```














