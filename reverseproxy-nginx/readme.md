# NGINX Reverse Proxy on EC2 for Docker Container

This repository provides a step-by-step guide to setting up an NGINX reverse proxy on an AWS EC2 instance for a Docker container running on port 8000.

## Prerequisites

- AWS account
- EC2 instance running Ubuntu
- SSH access to the EC2 instance
- add necessary ports to security inbound 
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

### Step 4: Install NGINX
Update the package list and install NGINX:

```sh
sudo apt update
sudo apt install nginx -y
```


### Step 5: Configure NGINX as a Reverse Proxy
Edit the NGINX configuration file to set up the reverse proxy for your Docker container running on port 8000:
```sh
sudo vim /etc/nginx/sites-available/default
```
Replace the existing configuration with the following:

```sh
server {
    listen 80;
    server_name PUBLICIP or domain name;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

**`or`**
<br> you can make a new configeration file for your app only thing you have to do is link the configeration files with this configeration

```sh
sudo vim /etc/nginx/sites-available/django-todo
```

write  configuration with the following:

```sh
server {
    listen 80;
    server_name PUBLICIP or Domain name;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
Link this configeration

```sh
sudo ln /etc/nginx/sites-available/django-todo /etc/nginx/sites-enabled
```

Save and exit the file.

### Step 6: Test and Restart NGINX
Test the NGINX configuration for syntax errors:

```sh
sudo nginx -t
```
![image](https://github.com/mayaworld13/proxy-server/assets/127987256/2112be65-fe39-4d26-b362-8921c7da056c)

If the test is successful, restart NGINX to apply the changes:

```sh
sudo systemctl restart nginx
```

### Step 7: Adjust Security Groups
Ensure that the security group associated with your EC2 instance allows inbound traffic on port 80 (HTTP).

Go to the EC2 Dashboard in the AWS Management Console.

```sh
Select your instance and navigate to the "Security" tab.
Edit the inbound rules of the security group associated with your instance.
Add a rule to allow HTTP traffic:
Type: HTTP
Protocol: TCP
Port Range: 80
Source: 0.0.0.0/0 (for testing purposes; restrict as needed)
```
### Step 8: Test the Reverse Proxy

Open a web browser and navigate to your EC2 instance's public IP or domain name. You should see the content served by your Docker container running on port 8000.

<p>
  <img src="https://github.com/mayaworld13/proxy-server/assets/127987256/15b00b58-0fc2-4fb7-8258-03dd7564ee95" alt="AWS VPC Project Diagram" width="800" height="400" />
</p>
---

## Troubleshooting

### 502 Bad Gateway Error
If you encounter a 502 Bad Gateway error, ensure the following:

   - Your Docker container is running and listening on port 8000.
   - NGINX can reach your Docker container.
   - Check the Docker container logs for errors:

   ```sh
   docker logs your_container_id
   ```

Ensure that NGINX is correctly configured and restart it if necessary:

```sh
sudo systemctl restart nginx
```

Check the NGINX error logs for more details:

```sh
sudo tail -f /var/log/nginx/error.log
```

### Conclusion
You have successfully set up an NGINX reverse proxy on an EC2 instance for a Docker container running on port 8000. This configuration allows you to manage incoming traffic and forward it to your application running inside the Docker container.








