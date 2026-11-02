# Spring PetClinic Microservices on Amazon ECS

A Java Spring Boot microservices project focused on decomposing the Spring PetClinic application into independently deployable services, containerising them with Docker, and deploying them on Amazon ECS.


## Project Overview

The project explores moving Spring PetClinic from a monolithic deployment model towards independently deployable Spring Boot microservices. Each service is packaged as a separate Docker image and designed to run as an ECS service.

The deployment uses Amazon ECS for container orchestration, Amazon ECR for image storage, Elastic Load Balancing for application traffic, and IAM roles for AWS permissions. Maven and the AWS CLI are used in the build and deployment workflow.

## Key Contributions

- Re-architected the Spring PetClinic monolith into independently deployable Spring Boot microservices.
- Packaged each microservice as its own Docker image for independent build and deployment.
- Deployed the services on Amazon ECS using ECS clusters, task definitions, and services.
- Configured Elastic Load Balancing to route incoming application traffic.
- Set up IAM roles and permissions for deployment and runtime access.
- Created a CI/CD workflow using Maven and the AWS CLI to automate application builds, image publishing, and deployment updates.

## Architecture

```text
                 Source Repository
                        |
                        v
                 Build Workflow
                  Maven + AWS CLI
                        |
              +---------+---------+
              |                   |
              v                   v
         Build Service A     Build Service B
              |                   |
              v                   v
         Docker Image A      Docker Image B
              |                   |
              +---------+---------+
                        |
                        v
                   Amazon ECR
                        |
                        v
                    Amazon ECS
                   (ECS Cluster)
                        |
              +---------+---------+
              |                   |
              v                   v
          ECS Service A       ECS Service B
          Task Definition     Task Definition
              |                   |
              +---------+---------+
                        |
                        v
             Elastic Load Balancing
                        |
                        v
                   Application
```

The diagram is a logical overview. Update service names and traffic routing to match the actual implementation.

## Technology Stack

- **Language:** Java
- **Framework:** Spring Boot
- **Build:** Maven
- **APIs:** REST
- **Containerisation:** Docker
- **Container orchestration:** Amazon ECS
- **Container registry:** Amazon ECR
- **Load balancing:** Elastic Load Balancing
- **Cloud and access management:** AWS, IAM
- **Automation:** AWS CLI, Maven
- **Version control:** Git, GitHub

## Microservices

The application is intended to be split into services with clear responsibilities and independent deployment boundaries.

For each service, document its actual responsibility, REST endpoints, port, configuration, and any communication with other services. Replace these placeholders with the service names and details present in the codebase.

| Service | Responsibility |
|---|---|
| Service A | Add the implemented business responsibility |
| Service B | Add the implemented business responsibility |

## Docker

Each Spring Boot service is packaged as a separate Docker image.

Typical build steps:

1. Build the application JAR with Maven.
2. Build a Docker image using the service's Dockerfile.
3. Tag the image with a release version or commit identifier.
4. Push the image to Amazon ECR.

Example commands (adjust paths and names):

```bash
mvn clean package
docker build -t <service-image>:<tag> <service-directory>
```

## Amazon ECS Deployment

Amazon ECS runs and manages the containerised services.

- **ECS cluster:** Groups the ECS services and tasks.
- **Task definition:** Specifies the container image, CPU and memory, port mappings, environment variables, logging, and IAM roles.
- **ECS service:** Maintains the desired number of running tasks and deploys task definition revisions.
- **Amazon ECR:** Stores the Docker images referenced by task definitions.

## Load Balancing and IAM

Elastic Load Balancing provides an entry point for application traffic and routes requests to configured targets. Configure listeners, target groups, health checks, and security groups according to the application.

IAM roles control access to AWS resources:

- The **task execution role** allows ECS to pull images and publish logs.
- The **task role** grants the application access to AWS services when required.
- The **CI/CD role** allows the deployment workflow to publish images and update ECS resources.

Use least-privilege permissions and do not commit credentials or secrets to the repository.

## CI/CD Workflow

The Maven- and AWS CLI-based workflow is intended to automate these stages:

1. Retrieve the source code from the configured Git repository.
2. Build and test the Spring Boot services using Maven.
3. Build separate Docker images for each service.
4. Authenticate with Amazon ECR and push the images.
5. Register updated ECS task definition revisions.
6. Update ECS services to deploy the new image versions.
7. Verify that the ECS services are stable and the application is healthy.

Example AWS CLI commands (replace placeholders with actual values):

```bash
aws sts get-caller-identity

aws ecr get-login-password --region <AWS_REGION> \
  | docker login --username AWS --password-stdin \
    <AWS_ACCOUNT_ID>.dkr.ecr.<AWS_REGION>.amazonaws.com

aws ecs describe-services \
  --cluster <ECS_CLUSTER_NAME> \
  --services <ECS_SERVICE_NAME> \
  --region <AWS_REGION>
```

## Prerequisites and Local Build

Required tools: Java (project-compatible version), Maven, Docker, and AWS CLI. The AWS identity used for deployment must have the necessary ECS, ECR, IAM, networking, and load-balancing permissions.

Build from the repository root:

```bash
mvn clean install
```

Run a service locally from its module:

```bash
mvn spring-boot:run
```

## Configuration

Keep environment-specific values outside source code. Typical settings include `AWS_REGION`, `ECR_REPOSITORY_URI`, `ECS_CLUSTER_NAME`, `ECS_SERVICE_NAME`, `IMAGE_TAG`, and the task-definition file path. Store credentials and secrets using a secure mechanism; never commit them to Git.

## Testing and Validation

- Confirm Maven compilation and tests pass.
- Verify each service starts and responds on its configured port.
- Confirm Docker images build and run.
- Verify ECS tasks reach `RUNNING` and services become stable.
- Check load-balancer target health and application endpoints.
- Run the CI/CD workflow and verify image publishing and deployment.

## Troubleshooting

- **ECS task stops:** Inspect the stopped-task reason, container exit code, logs, environment variables, ports, and memory settings.
- **Image pull fails:** Check the ECR URI and tag, repository access, and task execution role.
- **Unhealthy load-balancer target:** Check the health-check path, port, application readiness, and security groups.
- **Deployment fails:** Verify the AWS CLI identity and CI/CD role permissions.

## Future Improvements

Add automated integration tests, CloudWatch observability, ECS Service Auto Scaling, deployment rollback, and stronger secrets management.

---

**Project focus:** Spring Boot microservices, Docker containerisation, Amazon ECS deployment, Elastic Load Balancing, IAM, and CI/CD automation using Maven and the AWS CLI.
