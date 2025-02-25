# Problem 2: Building Castle In The Cloud

## Task

Architecture a highly available trading system with similar features to the Binance trading platform [https://www.binance.com](https://www.binance.com/en). This system will need to be resilient to failures, scalable, and cost-effective. You should design the architecture, choose the appropriate technologies, and explain your approach for maintaining high availability and scalability.

Due to time constraint, you will not be able to cover every feature on the reference platform, so choose some features that will help you demonstrate your mastery of cloud infrastructure.

### Deliverables

Your submission should include:

- An overview diagram of the services used and what role they play in the system.
- Elaboration on why each cloud service is used and what are the alternatives considered.
- Plans for scaling when the product grows beyond your current setup.

Specifications
You are limited to the following constraints for your plan.

| Cloud Provider    |  Amazon Web Services (AWS)   |
| ----------------- |------------------------------|
| Throughput        |   500 requests per second    |
| Response Time     |  p99 response time of <100ms |

## Answer

### System overview
![System Architecture Diagram](/src/problem2/system_architecture.drawio.png)

### What Cloud Services and Why

#### 1. Compute

- **Amazon ECS Fargate with Blue-Green Deployment and Auto Scaling:** Containerized services with zero-downtime deployments and Auto Scaling ensures sufficient capacity for handling traffic spikes. Blue/Green Deployment also helps with version control.
- **HAProxy** running on a low-cost EC2 instance in the same VPC as the ECS services.
- **AWS Lambda:** Executes background jobs asynchronously for lightweight, cost-effective processing.
Considerable alternatives to **ECS** could be **EKS (higher cost and complexity but has very good scalability and flexibility)** or **EC2 (higher maintenance overhead)**

#### 2. Database and Cache

- **Amazon RDS:** Provides high availability, automated backups, and read replicas for scalability.

- **Amazon ElastiCache (Redis):** Reduces database load by caching frequently accessed data, improving response times.

#### 3. Storage

**Amazon S3** :Stores static files, logs, reports, and backups reliably and cost-effectively.

#### 4. Networking & Security

- **AWS Application Load Balancer (ALB)** distributes incoming traffic across multiple ECS tasks, ensuring availability. Also, it is needed for Blue/Green deployment.
- **CloudFront with AWS WAFv2** ensures low-latency content delivery and caching
- **Route53 and Amazon Certificate Manager (ACM)** for Hosting and SSL/TSL certificate requests
- **API Gateway** to handle APIs
- **Amazon Cognito:** Securely manages user authentication and authorization.
- **Secret Manager, Parameter Store and Key Management Service** for encryption and secure storage of secrets.

#### 5. Monitoring & Logging

- **Amazon CloudWatch & AWS X-Ray:** Provides real-time monitoring, logging, and tracing. Container Insights and long log retention time is recommended.

- **AWS CloudTrail:** Enables auditing and tracking of user activity.

#### 6. Version Control and CI/CD

- **AWS CodeCommit** as Code repository
- **AWS CodePipeline, CodeBuild and CodeDeploy** for CI/CD, automatically builds and deploys new docker image to ECS whenever there's new code pushed to repository.
- **AWS ECR** to store Docker Images that are built from the code.

#### 7. Infrastructure Management

- **AWS CloudFormation** or **AWS CDK** to manage resources
- **Terraform** for Infrastructure-as-Code is a great alternative but i'm not sure if it's considered part of AWS Provider (following the Task's constraints)

### Scaling Strategies for when Product grows
- ECS Autoscaling Group to scale horizontally
- Buy reserved instances for better cost optimization
- spread containers across availability zones
- automatic scaling for Elasticache and RDS