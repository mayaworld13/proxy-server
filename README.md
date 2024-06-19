# NGINX Reverse Proxy on EC2 for Docker Container

This repository provides a step-by-step guide to setting up an NGINX reverse proxy on an AWS EC2 instance for a Docker container running on port 8000.

## Prerequisites

- AWS account
- EC2 instance running Ubuntu
- SSH access to the EC2 instance

## Setup Instructions

### Step 1: Launch an EC2 Instance

1. Log in to the AWS Management Console and navigate to the EC2 dashboard.
2. Launch a new EC2 instance using an Ubuntu AMI (e.g., Ubuntu 20.04 LTS).
3. Choose an instance type (e.g., `t2.micro` for free tier eligibility).
4. Configure instance details, add storage, and configure security groups:
   - Allow inbound traffic on ports 22 (SSH) and 80 (HTTP).
5. Review and launch the instance, then download the key pair for SSH access.

### Step 2: Connect to Your EC2 Instance

Use SSH to connect to your EC2 instance:

```sh
ssh -i /path/to/your-key.pem ubuntu@your-ec2-public-ip
```

### Step 3: Run Your Application which ever port you want to run 
In my case I am running my application using docker in port `8000`

```sh
sudo docker run -d -p 8000:8000 mayaworld13/django-todo-app
```
<p>
  <img src="https://github.com/mayaworld13/proxy-server/assets/127987256/9d31f985-d2db-4660-8aec-8e29bc9ec512" alt="AWS VPC Project Diagram" width="700" height="400" />
</p>





