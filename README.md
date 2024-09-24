# Multi-Container Dockerized React Application

This repository contains a multi-containerized React application deployed using **AWS Elastic Beanstalk**. The application uses **Docker** for containerization of multiple services and **TravisCI** for CI/CD automation. The project features various services like **Nginx**, **React Client**, **Node Server**, and a **Worker Service**. The infrastructure utilizes AWS services such as **RDS** and **ElastiCache** for database and caching.

## Table of Contents

- [Project Overview](#project-overview)
- [Technologies Used](#technologies-used)
- [File Structure](#file-structure)
- [Setup Instructions](#setup-instructions)
- [AWS Elastic Beanstalk Deployment](#aws-elastic-beanstalk-deployment)
- [Security and Networking](#security-and-networking)
- [CI/CD Integration](#cicd-integration)
- [Resources and Documentation](#resources-and-documentation)
- [Contributing](#contributing)

## Project Overview

This project demonstrates how to deploy a full-stack application in a multi-container environment using Docker and AWS Elastic Beanstalk. It integrates several services running in different containers, managed via a `docker-compose` configuration. This setup allows easy scalability and modular management of the application’s client, server, and worker processes.

### Features:
- Multi-container deployment using **Docker** and **AWS Elastic Beanstalk**.
- Integrated **RDS** and **ElastiCache** for handling data persistence and caching.
- Configurable environment variables for production deployments.
- Secure deployment using **IAM roles** and **Security Groups**.
- Automatic deployment using **TravisCI**.

## Technologies Used

- **React** (Frontend)
- **Node.js/Express** (Backend API)
- **Redis** (ElastiCache for caching)
- **PostgreSQL** (RDS for database)
- **Nginx** (Load balancing and reverse proxy)
- **AWS Elastic Beanstalk** (Deployment environment)
- **TravisCI** (CI/CD pipeline)

## File Structure

```bash
.
├── client                  # React frontend application
├── nginx                   # Nginx configuration for load balancing
├── server                  # Node.js backend API
├── worker                  # Worker service for background tasks
├── .travis.yml             # Travis CI configuration file
├── Dockerrun.aws.json      # AWS Elastic Beanstalk configuration file
├── docker-compose.yml      # Docker Compose configuration for production
├── docker-compose-dev.yml  # Docker Compose configuration for development
└── package.json            # Dependencies and scripts for client and server
```

## Setup Instructions
### Prerequisites
- **Docker**: Ensure Docker is installed on your machine.
- **AWS Elastic Beanstalk CLI**: Install AWS Elastic Beanstalk CLI to interact with your AWS environments.
- **AWS Account**: An active AWS account to deploy the application.
### Local Development
1. Clone the repository:
```bash
git clone https://github.com/firatbezir/multicontainer-docker.git
cd multicontainer-docker
```
2. Start the services locally using Docker Compose:
```bash
docker-compose -f docker-compose-dev.yml up --build
```
3. The application will be accessible at http://localhost:3000.

## Environment Variables

Define environment variables required for the app in your `.env` file:

```bash
POSTGRES_USER=your_username
POSTGRES_PASSWORD=your_password
REDIS_HOST=redis
```

## AWS Elastic Beanstalk Deployment
### Step-by-Step Instructions:
1. **Create Elastic Beanstalk Environment**: Navigate to the AWS Elastic Beanstalk console and create a new environment for a Docker-based application.

2. **Add Container Definitions**: Edit the `Dockerrun.aws.json` file to define the containers and their memory allocations:
```bash
{
    "AWSEBDockerrunVersion": 2,
    "containerDefinitions": [
        {
            "name": "client",
            "image": "firatbezir/multi-client",
            "hostname":"client",
            "essential":false,
            "memory": 128
        },
        {
            "name": "server",
            "image": "firatbezir/multi-server",
            "hostname":"api",
            "essential":false,
            "memory": 128   
        },
        {
            "name": "worker",
            "image": "firatbezir/multi-worker",
            "hostname":"worker",
            "essential":false,
            "memory": 128   
        },
        {
            "name": "nginx",
            "image": "firatbezir/multi-nginx",
            "hostname":"nginx",
            "essential":true,
            "portMappings": [
                {
                    "hostPort": 80,
                    "containerPort": 80
                }
            ],
            "links": ["client", "server"],
            "memory": 128   
        }
    ]
}
```
3. **Database & Caching Setup**: Create an **RDS PostgreSQL** database and an **ElastiCache Redis** instance from the AWS Management Console.

4. **Security Group Configuration**: Ensure that the security groups for your RDS and ElastiCache instances allow connections from your Elastic Beanstalk instance.

5. **Deploy via TravisCI**: Push your changes to the master branch, and TravisCI will automatically trigger the deployment based on the `.travis.yml` configuration.
