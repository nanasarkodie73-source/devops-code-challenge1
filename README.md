## Informative Project Summary

This project is a **containerized full-stack DevOps application** designed to demonstrate how a frontend and backend application can be developed locally, packaged with Docker, provisioned on AWS with Terraform, and continuously deployed using Jenkins. The architecture combines **GitHub, Docker, Terraform, Amazon ECS/Fargate, Amazon ECR, an Application Load Balancer, CloudWatch, IAM, and a Jenkins server running on EC2**. 

### Project Purpose

The main objective is to create a repeatable cloud deployment environment where application code can move from a GitHub repository through a CI/CD pipeline and ultimately run as containerized workloads in AWS. The application consists of:

* **Frontend:** Node/React-based application exposed on port **3000**
* **Backend:** Node-based application exposed on port **8080**
* **Containerization:** Separate Docker images for frontend and backend
* **Source control:** GitHub
* **Infrastructure as Code:** Terraform
* **Container registry:** Amazon ECR
* **Container platform:** Amazon ECS using Fargate
* **Load balancing:** AWS Application Load Balancer
* **CI/CD:** Jenkins
* **Monitoring/logging:** CloudWatch Logs and ECS Container Insights  

### Application Architecture

The application is designed as two independent containerized services. The frontend communicates with the backend through a configurable backend URL. During local Docker testing, the frontend can communicate with the backend through `host.docker.internal`. In the AWS environment, communication is instead routed through the Application Load Balancer. 

The AWS architecture uses a **VPC with public and private subnets across two Availability Zones**. Public resources such as the load balancer and Jenkins server are placed in public networking, while ECS application workloads are deployed in private subnets without public IP addresses. A NAT Gateway provides outbound connectivity for private resources. 

### AWS Infrastructure

Terraform manages the majority of the AWS infrastructure. The configuration includes:

* VPC and subnet architecture
* Internet Gateway and NAT Gateway
* Public and private route tables
* ECS cluster
* ECS Fargate task definitions and services
* ECR repositories
* Application Load Balancer and target groups
* Security groups
* IAM roles and policies
* CloudWatch log groups
* ECS auto scaling
* Jenkins EC2 instance
* Elastic IP for Jenkins   

The configured AWS region is **us-east-2**, with a VPC CIDR of **10.0.0.0/16**. The project uses the `devops-challenge` project name and identifies the environment as `production`. 

### Container and Deployment Model

Both application components are packaged as Docker images. The backend container uses Node.js 16 and listens on port 8080, while the frontend uses Node.js 18, builds the application, and serves the resulting static files on port 3000. 

Amazon ECR provides separate repositories for the frontend and backend images. Image scanning is enabled when images are pushed. ECS then uses those images as the basis for its Fargate tasks. 

The default resource configuration allocates **512 CPU units and 1,024 MB of memory** to each service. The desired task count is one, with automatic scaling configured between one and four tasks based on an average CPU target of **50%**. 

### Load Balancing and Networking

The Application Load Balancer provides the public entry point for the application. It listens on HTTP port 80 and forwards normal frontend traffic to the frontend target group. Requests matching `/api/*` are routed to the backend target group. 

Health checks are also configured:

* Frontend health check: `/`
* Backend health check: `/health`
* Expected HTTP status: `200`

This allows the load balancer to determine whether application tasks are healthy before directing traffic to them. 

### Security and Access

Security groups control communication between the AWS components. The frontend accepts traffic from the load balancer, while the backend accepts traffic from the frontend and load balancer. ECS workloads do not receive public IP addresses. 

The Jenkins security group in the provided configuration allows access on ports 22, 80, 443, and 8080. The document specifically notes that SSH and web access are configured with `0.0.0.0/0`; for a production environment, administrative access should be restricted to trusted IP ranges. 

IAM roles provide ECS with the permissions required for task execution and CloudWatch logging. 

### CI/CD Architecture

Jenkins acts as the automation engine connecting GitHub, Docker, AWS ECR, and ECS. The Jenkins pipeline is designed to:

1. Retrieve the application source code.
2. Build the frontend and backend Docker images.
3. Authenticate with Amazon ECR.
4. Tag and push the images to ECR.
5. Force new ECS deployments so the services use the updated images.
6. Clean up the Jenkins workspace afterward. 

Jenkins itself runs on an Amazon Linux 2023 EC2 instance. The configuration uses a **t3.small** instance with an encrypted **30 GB gp3** root volume. Docker is installed on the host, and Jenkins is configured to interact with Docker through the Docker socket. 

### Monitoring and Operational Visibility

The ECS cluster has **Container Insights enabled**, while separate CloudWatch log groups are created for the frontend and backend. The log groups have a seven-day retention period. This provides a basic mechanism for reviewing container behavior and troubleshooting deployments. 

Terraform outputs provide important infrastructure information such as the VPC ID, subnet IDs, ECR repository URLs, ALB DNS name, ECS cluster name, Jenkins public IP, and Jenkins URL. 

### Overall Architecture

Conceptually, the project can be viewed as:

**GitHub → Jenkins → Docker → Amazon ECR → Amazon ECS/Fargate → Application Load Balancer → Users**

Terraform supports the infrastructure layer underneath this workflow, while CloudWatch provides logging and ECS auto scaling provides the ability to adjust application capacity.  

### Expected Outcome

The completed project is intended to provide a publicly accessible, containerized application running on AWS. A successful deployment should have healthy frontend and backend ECS services, healthy ALB target groups, updated container images in ECR, working `/api/*` backend routing, and an application that produces the expected **“SUCCESS + GUID”** result. 

**In short:** this project demonstrates a practical **Infrastructure-as-Code + containerization + CI/CD + AWS deployment architecture**, with Terraform managing infrastructure, Docker packaging applications, Jenkins automating deployments, ECR storing images, ECS/Fargate running workloads, and the ALB exposing and routing the application.


Here is the Link to the Project: 
https://github.com/nanasarkodie73-source/devops-code-challenge1/blob/7f247baa9c6f12cc6a4bdf84b9f4d931c541e021/DevOps_Project_README_Steps_1-7.pdf# GitOps workflow test
