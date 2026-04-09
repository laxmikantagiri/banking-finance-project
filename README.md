
# Project Title

 FinanceMe is a Global leading Banking and Financial services provider based out of Germany. The company offers products and
 services like Banking, Funds Management, Loans, Debit Cards and Credits Cards, Investment Banking etc. Initially the company
 was using a Monolithic application architecture, As the company grown, It started facing difficulties in managing the application
 infrastructure and application deployments and Scaling of application when the traffic load increases. FinanceMe has decided to opt
 for microservice architecture for its applications and decided to go DevOps by implementing necessary automations using CICD.
 FinanceMe has decided to use AWS as primary cloud services provider to create servers, databases and application deployments.
 The company’s goal is to deliver the product updates frequently to production automatically with High quality & Reliability. They also
 want to accelerate software delivery speed, quality and reducing feedback time between developers and testers. Currently, they are
 facing following problems, because of various technologies involved in the project.


 This project will be about how to test the services and deploy code to dev/stage/prod etc, just on a click of button.

# Introduction
In this project, we implement a complete **end-to-end DevOps pipeline** to deploy a **Java-based banking application** using modern tools and best practices. The focus is on automating infrastructure provisioning, application build, and deployment using a **CI/CD approach**.

---

# Tech Stack Used

This project integrates multiple DevOps tools and technologies:

- **Version Control:** Git, GitHub
- **CI/CD:** Jenkins (can be replaced with GitHub Actions, GitLab CI, etc.)
- **Build Tool:** Maven
- **Containerization:** Docker
- **Infrastructure as Code:** Terraform
- **Configuration Management:** Ansible
- **Cloud Provider:** AWS (EC2, Security Groups, Key Pairs, etc.)

---

# Project Architecture Overview

The workflow begins with application source code hosted on GitHub. Infrastructure is provisioned dynamically using Terraform, and application deployment is fully automated using Jenkins pipelines, Docker, and Ansible.

---

# Step-by-Step Workflow

## 1. Source Code Management

The banking application source code is already available in a GitHub repository.

Users can simply fork the repository and update:

- GitHub username
- Docker Hub username

No need to write code from scratch — everything is pre-configured.

---

## 2. Jenkins Controller Setup

- A dedicated **AWS EC2 instance** is provisioned (via Terraform or manually).
- Required tools are installed:
    - Jenkins
    - Docker
    - Git
    - Maven
    - Terraform
    - Ansible

This instance acts as the **Jenkins Controller**.

---

## 3. CI/CD Pipeline Configuration (Jenkins)

Once Jenkins is set up, we configure a **pipeline** that automates the entire workflow.

### Pipeline StagesStage 1: Clone Repository

- Fetch the latest code from GitHub.

### Stage 2: Build Docker Image

- Use Maven to build the Java application.
- Package the application into a Docker image.

### Stage 3: Push to Docker Hub

- Tag and push the Docker image to Docker Hub repository.

### Stage 4: Terraform Initialization & Plan

- Create a workspace (e.g., `test`)
- Initialize Terraform
- Generate execution plan

### Stage 5: Deploy to Test Environment

- Apply Terraform plan to provision infrastructure
- Deploy application to test server

### Stage 6: Promote to Production

- If test deployment is successful:
    - Switch workspace to `prod`
    - Apply the same Terraform configuration for production

---

# Infrastructure Provisioning (Terraform)

All infrastructure is provisioned using **Terraform**, ensuring repeatability and scalability.

### Resources Created:

- EC2 Instances
- Security Groups
- Key Pairs
- Dynamic AMI selection (latest available images)

### Workspaces Used:

- `test` → staging/testing environment
- `prod` → production environment

---

# Configuration Management (Ansible)

After provisioning infrastructure:

- Terraform uses **provisioners** to prepare instances for Ansible.
- Ansible is then triggered to configure the servers.

### Ansible Responsibilities:

- Update system packages
- Install Docker
- Configure runtime environment
- Pull latest Docker image
- Run containerized application

👉 This demonstrates the **clear distinction and collaboration** between:

- Terraform → Infrastructure provisioning
- Ansible → Configuration & deployment

---

# Application Deployment (Docker)

- Jenkins builds the Docker image
- Pushes it to Docker Hub
- Ansible pulls the latest image on target servers
- Runs the containerized banking application

---

# CI/CD Flow Summary

```
GitHub → Jenkins Pipeline → Docker Build → Docker Hub → Terraform → AWS EC2 → Ansible → Application Deployment
```

---

# Key Highlights

- Fully automated **CI/CD pipeline**
- Infrastructure managed as code (**Terraform**)
- Configuration managed via **Ansible**
- Containerized deployment using **Docker**
- Environment separation using **Terraform Workspaces**
- Production-ready workflow design

---

# Flexibility

Although this project uses **Jenkins**, the same pipeline logic can be implemented using:

- GitHub Actions
- GitLab CI/CD
- Azure DevOps Pipelines

---

# Learning Outcomes

By completing this project, you will gain hands-on experience in:

- Designing real-world CI/CD pipelines
- Automating infrastructure provisioning
- Managing multi-environment deployments
- Integrating multiple DevOps tools seamlessly
- Understanding the roles of Terraform vs Ansible

---

# Getting Started

1. Fork the repository
2. Update your:
    - GitHub username
    - Docker Hub username
3. Run the pipeline in Jenkins
4. Watch your infrastructure and application deploy automatically 🎉

# Implementation

Go to the AWS console and create an ec2 instance for the jenkins controller.

If you are in free tier then `c7i-flex.large` will work, or can take t3a.medium with 20 gb disk space.

> [!NOTE]
>
> I am taking ubuntu image here


Once the instance is provisioned.

SSH to the instance and then run the follow steps.

1. Update the instance
    
    ```bash
    sudo apt update
    ```
    
2. Install jenkins 
    
    Docs: https://www.jenkins.io/doc/book/installing/linux/#debianubuntu
    
    Install Java
    
    ```bash
    sudo apt install fontconfig openjdk-21-jre
    java -version
    ```
    
    Install Jenkins:
    
    ```bash
    sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
      https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
    echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
      https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
      /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt update
    sudo apt install jenkins
    ```
    
    Verify:
    
    ```python
    jenkins --version
    ```
    
3. Install Maven
    
    ```python
    sudo apt install maven -y
    ```
    
    Verify:
    
    ```python
    mvn --version
    ```
    
4. Install Terraform
    
    Docs: https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli
    
    ```bash
    sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
    
    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null
       
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
    
    sudo apt update
    sudo apt-get install terraform
    
    terraform --version
    ```
    
5. Install Ansible:
    
    Docs: https://docs.ansible.com/projects/ansible/latest/installation_guide/installation_distros.html
    
    ```python
    sudo apt update
    sudo apt install software-properties-common
    sudo add-apt-repository --yes --update ppa:ansible/ansible
    sudo apt install ansible
    ```
    
    Verify:
    
    ```python
    ansible --version
    ```
    
6. Install Docker: 
    
    ```bash
    # Add Docker's official GPG key:
    sudo apt update
    sudo apt install ca-certificates curl
    sudo install -m 0755 -d /etc/apt/keyrings
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    sudo chmod a+r /etc/apt/keyrings/docker.asc
    
    # Add the repository to Apt sources:
    sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
    Types: deb
    URIs: https://download.docker.com/linux/ubuntu
    Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
    Components: stable
    Architectures: $(dpkg --print-architecture)
    Signed-By: /etc/apt/keyrings/docker.asc
    EOF
    
    sudo apt update
    sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
    
    ```bash
    sudo systemctl status docker
    ```
    
    Add your user to the `docker` group:
    
    ```bash
    sudo usermod -aG docker $USER
    sudo usermod -aG docker jenkins
    
    # Activate the changes to the group
    newgrp docker
    ```
    

Allow TCP port 8080 for jenkins in SG.

Access the jenkins UI in the browser

```python
publicIP:8080
```

Unlock jenkins by adding the password

Password can be found the below directory:

```python
/var/lib/jenkins/secrets/initialAdminPassword
```

Click on `install suggested plugin` in the UI

Create the Admin user and the save and continue.

# Testing the App

Before create the pipeline let us test the app locally, So that if we find any error in the app level we can fix it quickly. 

Clone the repo

`cd` to the repo and build the app using the below command

```bash
mvn clean package
```

It creates a directory `target` in which the compiled code are stored.

Create the image and run:

```bash
docker build -t banking-app .
```

Verify the image:

```bash
docker images
```

Run the image:

```bash
docker run -idp 8081:8081 banking-app:latest
```

The application runs on TCP port `8081`

To access it you have to allow the same port in the SG inbound rule.

Then access it:

```bash
publicIP:8081
```

![image.png](/assets/image01.png)

Once success you can stop the app in the jenkins controller and remove the port 8081 from the inbound rule in the SG.

```bash
docker stop <cont.ID>
docker rm <cont. ID>
```

# Setting up Jenkins

We have to add credentials in the jenkins as we need docker hub creds to push and and aws api keys to create the infras. 

Jenkins store that as env vars we just need reference the secrets. No need to hard code anywhere.

- Create access keys in the aws console. → by giving ec2 full access permission to the user
- Go to the Settings → Credentials → Add Credentials
- Choose secret text for aws API access keys.
- Create a secret and name it `aws-access-key-id` for the `access key`
- Create another one for `secret-access-key` and name it  `aws-secret-access-key`
    
    ![image.png](/assets/image02.png)
    
- Create a credential type of user name and password for the docker hub.
- Login to your docker hub and create a PAT and use that as password.
- Give the id as `docker-creds` for the credential.
- Once done you should be having 3 creds available.
    
    ![image.png](/assets/image03.png)
    

Go back to the home page and create an `item` → Choose item type as `Pipeline` → Name it as `banking` → `Ok`

Scroll down to the pipeline section:

- Chhose definition a `pipeline script from SCM`
- Select SCM as Git.
- Add the repo URL
- Provide credentials if its private repo.
- Specify the branch where the `jenkinsfile` file available
    
    ![image.png](/assets/image04.png)
    
- Scroll a bit Up and enable the  `GitHub hook trigger for GITScm polling`  in the Triggers section for github webhook setup.
- Make sure to check the `jenkinsfile`, `main.tf` and `ansible playbook.yaml` and replace the necessary changes. such as - gtihub repo, docker hub account name etc.
- In the `main.tf` we are creating a key pair for the servers. So we need manually create the key in for jenkins user.
- in the jenkins controller swtich to the jenkins user and do
    
    ```bash
    sudo su jenkins
    ssh-keygen
    ```
    
    verify:
    
    ```bash
    ls ~/.ssh/
    ```
    
- Go back to the jenkins UI click on build now on the created `banking` project.

- Check the console output for the logs.
- Once done you will notice there will  be 2 instances available in `ap-south-1` region.
- Copy the instance public ip and access it directly in the browser with the TCP port `8081`

```bash
PublicIP:8081
```

![image.png](/assets/image05.png)

# WebHook Integration

- We already added the configure the pipeline , rest we need to configure it in the github.
- In the github repo → Go to the settings → WebHook → Create a new webhook
- In the payload URL add the jenkins server public ip with http header and port and last mention `github-webhook/`  and save.
- For example :
    
    ```bash
    http://<PUBLICIP>:8080/github-webhook/
    ```
    
    ![image.png](/assets/image06.png)
    
- Now change some content in any file in the repo and commit it.
- After commiting you will notice jenkins automatically started building the pipeline.
- In the console output it also mentions below for the webhook trigger.
    
    ```bash
    Started by GitHub push by laxmikantagiri
    ```
    



# Clean Up

- Edit the jenkins file and remove the terraform apply stage and build that again, additinally you can create another pipeline for cleaning up the resources or you can use parameterized pipeline in jenkins.
- Next, delete the jenkins controller manually.
- Delete the webhook in the github repo.



## Overview
![Description of GIF](./assets/gifproj.gif)
