# Part 1

* Design a URL Shortener - [Video](https://www.youtube.com/watch?v=fMZMm_0ZhK4)

* Design a High Throughput Logging System- [Video](https://youtu.be/ak1wTYq_opQ?si=n6i5Gl4_6k4dO-jF)

* Design a Social Media platform
  - [Twitter](https://www.youtube.com/watch?v=wYk0xPP_P_8)
  - [Instagram](https://www.youtube.com/watch?v=VJpfO6KdyWE)
  - [Facebook](https://www.youtube.com/watch?v=9-hjBGxuiEs)
  - [TikTok](https://www.youtube.com/watch?v=Z-0g_aJL5Fw)

* Design a Chat System 
  - [Whatsapp](https://www.youtube.com/watch?v=vvhC64hQZMk)
  - [Slack](https://systemdesign.one/slack-architecture/)

* Design a Web Crawler - [Video](https://www.youtube.com/watch?v=BKZxZwUgL3Y&ab_channel=TechDummiesNarendraL)

* Design a video streaming service 
  - [Youtube](https://www.youtube.com/watch?v=jPKTo1iGQiE)
  - [Netflix](https://www.youtube.com/watch?v=psQzyFfsUGU)

* Design an E-Commerce platform like Amazon - [Video](https://www.youtube.com/watch?v=EpASu_1dUdE)

* Design a Notification System - [Video](https://www.youtube.com/watch?v=CUwt9_l0DOg)

* Design a ride sharing service like Uber/Lyft - [Video](https://www.youtube.com/watch?v=umWABit-wbk)

* [Design a Key Value Store](https://github.com/r-shreesha/Interview-Prep/blob/main/System_Design/KV_Store.md) - [Video](https://www.youtube.com/watch?v=rnZmdmlR-2M)

* Design a Scalable Logging and Monitoring System - [Video](https://www.youtube.com/watch?v=kIcq1_pBQSY)


# Part 2

* [Design a UID Generator](https://github.com/r-shreesha/Interview-Prep/blob/main/System_Design/uid_gen.md)

* [Design a S3 Object Storage](https://github.com/r-shreesha/Interview-Prep/blob/main/System_Design/S3/README.md) - [Video](https://www.youtube.com/watch?v=UmWtcgC96X8)

* Design a Distributed Message Queue like Kafka - [Video](https://www.youtube.com/watch?v=iJLL-KPqBpM)

* Design a Rate limiter - [Video](https://www.youtube.com/watch?v=mhUQe4BKZXs)

* Design a Search Auto complete system - [Video](https://www.youtube.com/watch?v=us0qySiUsGU)

* Design Google Drive
  - [Dropbox](https://www.youtube.com/watch?v=U0xTu6E2CT8)
  - [Google Docs](https://www.youtube.com/watch?v=2auwirNBvGg)
  - [Google Maps](https://www.youtube.com/watch?v=jk3yvVfNvds)

* Design a Stock Exchange - [Video](https://www.youtube.com/watch?v=dUMWMZmMsVE)

* Design a News Feed System

* Design Proximity Service

* Design Nearby Friends

* Design an Ad Click Event Aggregation

* Design a Hotel Reservation System

* Design a Distributed Email Service

* Design a Real-time Gaming Leaderboard

# CI/CD Pipeline Design

## Question
Design a CI/CD pipeline for deploying machine learning models. How would you integrate automated testing and rollback mechanisms in the pipeline?

### High-Level Design
- **Source Control**: Code repository (e.g., GitHub, GitLab).
- **CI Server**: Jenkins, AWS CodePipeline.
- **Build Stage**: Docker for containerization.
- **Test Stage**: Automated testing frameworks (e.g., pytest, TensorFlow Testing).
- **Deploy Stage**: Kubernetes, AWS ECS/EKS.
- **Monitoring and Rollback**: Prometheus, Grafana, AWS CloudWatch, and rollback scripts.

### Low-Level Design
- **Source Control**:
  - Use branching strategies like GitFlow.
  - Implement code reviews and pull request checks.
- **CI Server**:
  - Jenkins with pipelines as code (Jenkinsfile).
  - AWS CodePipeline integrated with AWS CodeBuild and CodeDeploy.
- **Build Stage**:
  - Dockerfile to containerize the application.
  - Use multi-stage builds to optimize Docker images.
- **Test Stage**:
  - Unit tests, integration tests, and end-to-end tests.
  - Utilize frameworks like pytest for Python-based ML models.
- **Deploy Stage**:
  - Kubernetes manifests or Helm charts for deployment.
  - AWS ECS/EKS for scalable deployment.
- **Monitoring and Rollback**:
  - Prometheus for metrics collection.
  - Grafana for visualization.
  - AWS CloudWatch for logs and alerts.
  - Automated rollback using scripts triggered by monitoring alerts.

# Automation for Operations

## Question
Design a system to automate the deployment and monitoring of ML servers. How would you ensure the system is scalable and reliable?

### High-Level Design
- **Infrastructure Provisioning**: Terraform, AWS CloudFormation.
- **Configuration Management**: Ansible, Chef.
- **Deployment**: Jenkins, AWS CodeDeploy.
- **Monitoring**: Prometheus, Grafana, AWS CloudWatch.
- **Alerting**: PagerDuty, AWS SNS.

### Low-Level Design
- **Infrastructure Provisioning**:
  - Use Terraform scripts to provision EC2 instances, VPCs, subnets, security groups.
  - AWS CloudFormation templates for AWS-specific resources.
- **Configuration Management**:
  - Ansible playbooks to configure servers.
  - Use roles and tasks for modular configuration.
- **Deployment**:
  - Jenkins pipelines to trigger Ansible playbooks and deploy application code.
  - AWS CodeDeploy to manage deployment lifecycle.
- **Monitoring**:
  - Prometheus to collect metrics.
  - Grafana dashboards to visualize metrics.
  - AWS CloudWatch for custom logs and alarms.
- **Alerting**:
  - PagerDuty for on-call alerts.
  - AWS SNS for notification distribution.

# Release Management System

## Question
Design a release management system that handles multiple environments (dev, staging, production). Discuss strategies for handling version control, rollback, and auditing.

### High-Level Design
- **Version Control**: Git branching strategy (GitFlow).
- **Environment Management**: Separate environments for dev, staging, and production.
- **CI/CD Pipelines**: Jenkins, AWS CodePipeline for each environment.
- **Rollback Mechanism**: Blue/Green Deployments, Canary Releases.
- **Auditing**: Logging, Monitoring, and Audit Trails.

### Low-Level Design
- **Version Control**:
  - GitFlow strategy with branches for feature, develop, release, and hotfix.
  - Use tags for versioning releases.
- **Environment Management**:
  - Separate AWS accounts or VPCs for dev, staging, and production.
  - Use infrastructure as code (Terraform) for consistent environment setup.
- **CI/CD Pipelines**:
  - Jenkins pipelines for each environment.
  - AWS CodePipeline with stages for build, test, and deploy.  
- **Rollback Mechanism**:
  - Implement Blue/Green deployment strategy to switch between versions with minimal downtime.
  - Canary releases to test new versions on a subset of users before full deployment.
- **Auditing**:
  - Use AWS CloudTrail for API activity logging.
  - Implement logging frameworks (e.g., ELK stack) for application logs.
  - Regular security audits and compliance checks.

# AWS Step Functions Workflow

## Question
How would you design a workflow using AWS Step Functions to automate a complex multi-step process? Integrate AWS Lambda, DynamoDB, and other AWS services in the design.

### High-Level Design
- **Step Functions Workflow**: Define states for each step.
- **Lambda Functions**: Implement business logic.
- **DynamoDB**: Store intermediate and final results.
- **S3**: Input and output data storage.
- **CloudWatch**: Monitoring and logging.

### Low-Level Design
- **Step Functions Workflow**:
  - Define states: Task, Choice, Parallel, and Catch.
  - Use JSON/YAML to define the workflow.
- **Lambda Functions**:
  - Implement functions for each task.
  - Use environment variables and IAM roles for configuration and permissions.
- **DynamoDB**:
  - Tables to store intermediate states and results.
  - Use partition keys for efficient queries.
- **S3**:
  - Buckets for input data, intermediate results, and output data.
  - Set up lifecycle policies for data management.
- **CloudWatch**:
  - Create metrics and alarms for monitoring Lambda functions and workflow execution.
  - Use CloudWatch Logs for debugging and analysis.

# Machine Learning Infrastructure

## Question
Design the infrastructure for deploying and managing machine learning models in production. Consider factors such as model versioning, monitoring, and scalability.

### High-Level Design
- **Model Versioning**: MLflow, DVC.
- **Deployment**: Kubernetes, AWS SageMaker.
- **Scalability**: Auto-scaling with Kubernetes or AWS services.
- **Monitoring**: Prometheus, Grafana, AWS CloudWatch.
- **CI/CD for Models**: Jenkins, GitHub Actions.

### Low-Level Design
- **Model Versioning**:
  - Use MLflow to track experiments and model versions.
  - DVC for data versioning and reproducibility.
- **Deployment**:
  - Kubernetes with Helm charts for model deployment.
  - AWS SageMaker for managed model deployment and scaling.
- **Scalability**:
  - Use Kubernetes Horizontal Pod Autoscaler.
  - AWS Auto Scaling Groups for EC2 instances.
- **Monitoring**:
  - Prometheus for custom metrics.
  - Grafana dashboards for visualization.
  - AWS CloudWatch for logs and alarms.
- **CI/CD for Models**:
  - Jenkins pipeline to automate model training, testing, and deployment.
  - GitHub Actions for integration with version control.
