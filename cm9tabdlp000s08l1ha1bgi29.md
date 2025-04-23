---
title: "Automating EKS Deployment with Terraform and GitHub Actions – The DevOps Way"
datePublished: Wed Apr 23 2025 01:58:17 GMT+0000 (Coordinated Universal Time)
cuid: cm9tabdlp000s08l1ha1bgi29
slug: automating-eks-deployment-with-terraform-and-github-actions-the-devops-way
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1745370889486/4f281469-da33-4ae1-be68-4266d85e8c32.png
tags: docker, github, sonarqube, kubernetes, maven, terraform, githubpages, github-actions-1, selfhosted, gitleaks, devsecops-cicd-pipeline-sonarqube-trivy-owasp-jenkins-security-continuousintegration-continuousdeployment-automation-codequality-containersecurity-vulnerabilityscanning-securecoding-softwaredevelopment-devops-cybersecurity-applicationsecurity-softwaresecurity-techblog-itsecurity-codeanalysis-securitytools, docker-kubernetes-aws-jenkins-prometheus-grafana-sonarqube-trivy-helm-argocd, sonarqube-quality-gate, trivy-container-scan, devopsjenkins-kubernetes-cicd-aws-maven-sonarqube-trivy-nexus-springboot-monitoring-grafana-prometheus-rbac-kubeadm-cloudcomputing-devopsjourney

---

[https://github.com/user-attachments/assets/b93fa528-6778-4c97-94ee-6e36c7f4fa8e](https://github.com/user-attachments/assets/b93fa528-6778-4c97-94ee-6e36c7f4fa8e)

![Image](https://github.com/user-attachments/assets/1441af6c-9a1a-4bdd-97bd-1197519455c5 align="left")

## **Project Overview**

This project outlines the step-by-step process of setting up a CI/CD pipeline using GitHub Actions. It shows how to automate an application's build, test, and deployment to Kubernetes using tools like Docker, Trivy, SonarQube, and Terraform. The project also highlights the integration of AWS roles for managing cloud resources and Kubernetes clusters.

---

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

* Terraform is installed on your machine.
    
* A GitHub account.
    
* A GitHub personal access token with the necessary permissions to create repositories.
    

> ⚠️ **Important:**

> 1. Make sure First you will create a `.pem` key manually from the AWS console. i.e "MYLABKEY.pem" because it will be used for creating `EC2` VMs and `EKS cluster`.
>     
> 2. Copy `MYLABKEY.pem` in the terraform directory (`01.Code_IAC_Selfhosted-Runner-and-Trivy` and `03.Code_IAC_Terraform_box` ) as below your terraform code
>     
> 3. [Generate the Github Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
>     

```sh
ls 
\Learning_GitHub_Action\01.Github_Action_DevOps-Project\Terraform_Code_Infra_setup


Mode                 LastWriteTime         Length Name                                                                                                                                                                                              
----                 -------------         ------ ----                                                                                                                                                                                              
dar--l          17/04/25  12:48 PM                .terraform                                                                                                                                                                                        
dar--l          21/04/25  12:34 PM                00.Code_IAC-github-repo                                                                                                                                                                           
dar--l          21/04/25  12:34 PM                01.Code_IAC_Selfhosted-Runner-and-Trivy                                                                                                                                                           
dar--l          21/04/25   1:38 PM                02.Code_IAC_SonarQube                                                                                                                                                                             
dar--l          21/04/25  12:34 PM                03.Code_IAC_Terraform_box                                                                                                                                                                         
-a---l          20/08/24   1:45 PM            493 .gitignore                                                                                                                                                                                                                                                                                                                                    
-a---l          21/04/25   1:59 PM          18225 AboutThis Project.md                                                                                                                                                                              
-a---l          19/04/25   8:48 PM           1309 main.tf
```

* [Clone the repository for the Terraform code](https://github.com/mrbalraj007/Learning_GitHub_Action/tree/main/01.Github_Action_DevOps-Project/Terraform_Code_Infra_setup)  
    
    > 💡 **Note:** Replace GitHub Token, resource names and variables as per your requirement in terraform code
    > 
    > * For `github Repo` Token value to be updated in file - `00.Code_IAC-github-repo/`[`variables.tf`](http://variables.tf) (i.e default- `xxxxxx`\*)
    >     
    > * **For EC2 VM** - `01.Code_IAC_Selfhosted-Runner-and-Trivy/`[`main.tf`](http://main.tf) (i.e keyname- `MYLABKEY`*) -* `03.Code_IAC_Terraform_box/`[`main.tf`](http://main.tf) (i.e keyname- `MYLABKEY`)
    >     
    > * For **Cluster name** - `03.Code_IAC_Terraform_box/k8s_setup_file/`[`main.tf`](http://main.tf) (i.e `balraj`\*).
    >     
    > * For **Node Pod** - `03.Code_IAC_Terraform_box/k8s_setup_file/`[`variable.tf`](http://variable.tf) (i.e `MYLABKEY`\*)
    >     
    
* **Set up your GitHub token**:
    
    * Create a new GitHub personal access token with the `repo` scope at [https://github.com/settings/tokens](https://github.com/settings/tokens).
        
    * Then set it as an environment variable (DO NOT commit your token to version control):
        
    
    ```bash
    # For Linux/macOS
    export GITHUB_TOKEN=your_github_token
    
    # For Windows Command Prompt
    set GITHUB_TOKEN=your_github_token
    
    # For Windows PowerShell (I used this one)
    # $env:GITHUB_TOKEN="your_github_token"
    $env:TF_VAR_github_token = "your-github-personal-access-token"
    ```
    
* **Test and verify with curl again in powershell terminal:**
    
    ```powershell
    $headers = @{
     Authorization = "token $env:TF_VAR_github_token"
    }
    Invoke-WebRequest -Uri "https://api.github.com/user" -Headers $headers
    ```
    
    * You should see your GitHub user info in JSON, **not** "Bad credentials".
        

---

## **Key Points**

1. **GitHub Actions Overview**:
    
    * GitHub Actions is used as the CI/CD tool for this project.
        
    * It eliminates the need for setting up and maintaining Jenkins servers by providing managed runners.
        
2. **Pipeline Stages**:
    
    * **Compile**: Builds the application.
        
    * **Security Checks**: Scans for vulnerabilities using Trivy and GitLeaks.
        
    * **Unit Testing**: Executes test cases to ensure code quality.
        
    * **Build and Publish Docker Image**: Builds a Docker image and uploads it as an artifact.
        
    * **Deploy to Kubernetes**: Deploys the application to an EKS cluster using Terraform.
        
3. **Tools and Technologies Used**:
    
    * **GitHub Actions**: CI/CD automation.
        
    * **Docker**: Containerization of the application.
        
    * **Trivy**: Security scanning for vulnerabilities.
        
    * **GitLeaks**: Detects hardcoded secrets in the source code.
        
    * **SonarQube**: Code quality analysis.
        
    * **AWS CLI**: Manages AWS resources.
        
    * **Terraform**: Infrastructure as Code (IaC) for provisioning EKS clusters.
        
    * **Kubernetes**: Orchestrates containerized applications.
        
4. **Why Use This Project**:
    
    * Automates the software delivery process.
        
    * Ensures code quality and security through automated checks.
        
    * Simplifies deployment to Kubernetes clusters.
        
    * Demonstrates best practices for CI/CD pipelines.
        
5. **Takeaways**:
    
    * Understanding of GitHub Actions and its capabilities.
        
    * Hands-on experience with integrating security tools like Trivy and GitLeaks.
        
    * Knowledge of deploying applications to Kubernetes using Terraform.
        
    * Insights into managing AWS resources with AWS CLI.
        

---

## **Step-by-Step Process**

### Setting Up the Infrastructure

I have created a Terraform code to set up the entire infrastructure, including the installation of required applications, tools, and the EKS cluster automatically created.

* ⇒ Docker Install
    
* ⇒ SonarQube Install
    
* ⇒ Trivy Install
    
* ⇒ Terraform Install
    
* ⇒ EKS Cluster Setup
    

> 💡 **Note:** ⇒ `EKS cluster` creation will take approx. 10 to 15 minutes.

#### To Create EC2 Instances

First, we'll create the necessary virtual machines using `terraform` code.

Below is a Terraform code:

Once you [clone repo](https://github.com/mrbalraj007/Learning_GitHub_Action/blob/main/01.Github_Action_DevOps-Project/Terraform_Code_Infra_setup), then go tothe folder *"<mark>01.Github_Action_DevOps-Project/Terraform_Code_Infra_setup</mark>"* and run the terraform command.

```bash
cd 01.Github_Action_DevOps-Project/Terraform_Code_Infra_setup

$ ls

 00.Code_IAC-github-repo/   01.Code_IAC_Selfhosted-Runner-and-Trivy/   02.Code_IAC_SonarQube/   03.Code_IAC_Terraform_box/  'AboutThis Project.md'   main.tf
```

> 💡 **Note:** ⇒ Make sure to run [`main.tf`](http://main.tf) which is located outside of the folder. I have created the code in such a way that a single file will call all of the folders.

```bash
 ls -la
total 72
-rw-r--r-- 1 bsingh 1049089   493 Aug 20  2024  .gitignore
drwxr-xr-x 1 bsingh 1049089     0 Apr 21 12:34  00.Code_IAC-github-repo/
drwxr-xr-x 1 bsingh 1049089     0 Apr 21 12:34  01.Code_IAC_Selfhosted-Runner-and-Trivy/
drwxr-xr-x 1 bsingh 1049089     0 Apr 21 13:38  02.Code_IAC_SonarQube/
drwxr-xr-x 1 bsingh 1049089     0 Apr 21 12:34  03.Code_IAC_Terraform_box/
-rw-r--r-- 1 bsingh 1049089 21284 Apr 21 14:44 'AboutThis Project.md'
-rw-r--r-- 1 bsingh 1049089  1309 Apr 19 20:48  main.tf
```

You need to run [`main.tf`](http://main.tf) file using following terraform command.

Now, run the following command.

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply 
# Optional <terraform apply --auto-approve>
```

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372263774/ad91ad2e-493c-473b-81c7-236ac5635fba.png align="center")

Once you run the Terraform command, we will verify the following things to ensure everything is set up properly using Terraform.

### Inspect the `Cloud-Init` logs:

Once connected to the EC2 instance then you can check the status of the `user_data` Script by inspecting the [log files](https://github.com/mrbalraj007/Learning_GitHub_Action/blob/main/01.Github_Action_DevOps-Project/Terraform_Code_Infra_setup/03.Code_IAC_Terraform_box/cloud-init-output.log).

```bash
# Primary log file for cloud-init
sudo tail -f /var/log/cloud-init-output.log
                    or 
sudo cat /var/log/cloud-init-output.log | more
```

> 🔍- *If the user\_data script runs successfully, you will see output logs and any errors encountered during execution.*

> 🔍- *If there’s an error, this log will provide clues about what failed.*

* Verify the Outcome of "`cloud-init-output.log`"
    

### Verify the Installation

* \[x\] Docker version
    

```bash
ubuntu@ip-172-31-95-197:~$ docker --version
Docker version 24.0.7, build 24.0.7-0ubuntu4.1


docker ps -a
ubuntu@ip-172-31-94-25:~$ docker ps
```

* \[x\] Trivy version
    

```bash
ubuntu@ip-172-31-89-97:~$ trivy version
Version: 0.55.2
```

* \[x\] Terraform version
    

```bash
ubuntu@ip-172-31-89-97:~$ terraform version
Terraform v1.9.6
on linux_amd64
```

* \[x\] eksctl version
    

```bash
ubuntu@ip-172-31-89-97:~$ eksctl version
0.191.0
```

* \[x\] kubectl version
    

```bash
ubuntu@ip-172-31-89-97:~$ kubectl version
Client Version: v1.31.1
Kustomize Version: v5.4.2
```

* \[x\] AWS CLI version
    

```bash
ubuntu@ip-172-31-89-97:~$ aws version
usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run:
  aws help
  aws <command> help
  aws <command> <subcommand> help
```

### Verify the EKS Cluster installation

* Will take a putty session from Terraform EC2
    
* On the `terraform` virtual machine, go to the directory `k8s_setup_file` and open the file `cat apply.log` to verify whether the cluster is created or not.
    
* Will verify the cluster status from
    
    * `sudo cat /var/log/cloud-init-output.log | more` or
        
    * `cat /home/ubuntu/k8s_setup_file/apply.log`
        
    
    ```sh
    ubuntu@ip-172-31-90-126:~/k8s_setup_file$ pwd
    /home/ubuntu/k8s_setup_file
    ubuntu@ip-172-31-90-126:~/k8s_setup_file$ cd ..
    ```
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372332000/cbc2c5e9-ddda-4a99-9e58-3bf51dcaf25e.png align="center")
    
* After Terraform deploys on the instance, it's time to set up the cluster. If you log out of the SSH session, reconnect and run the following command:
    
    ```bash
    aws eks update-kubeconfig --name <cluster-name> --region 
    <region>
    ```
    
* Once the EKS cluster is set up, you need to run the following command to interact with EKS.
    
    ```sh
    aws eks update-kubeconfig --name balraj-cluster --region us-east-1
    ```
    

> > ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372378008/36d25c16-60b0-4c98-bf4e-a0ea3dfb64cc.png align="center")
> > 
> > ⚠️ **Important:**  
> > *The* `aws eks update-kubeconfig` command is used to configure your local kubectl tool to interact with an Amazon EKS (Elastic Kubernetes Service) cluster. It updates or creates a kubeconfig file that contains the necessary authentication information to allow kubectl to communicate with your specified EKS cluster.

> > **What happens when you run this command**:  
> > The AWS CLI retrieves the required connection information for the EKS cluster (such as the API server endpoint and certificate) and updates the kubeconfig file located at `~/.kube/config (by default)`. It configures the authentication details needed to connect kubectl to your EKS cluster using IAM roles. After running this command, you will be able to interact with your EKS cluster using kubectl commands, such as `kubectl get nodes` or `kubectl get pods`.

```sh
kubectl get nodes
kubectl cluster-info
kubectl config get-contexts
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372412778/33d31290-28c5-48d3-96a2-636261b97585.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372426074/9ca74899-038e-455e-8cc0-a399c09bbbf2.png align="center")

---

### **Verify GitHub Repo and GitHub Actions**

* Verify that the GitHub repository is created and initialise it since we are using Terraform.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372458707/afbaf9d8-54ea-4717-a99e-75126be00f45.png align="center")
    
* Verify a `.github/workflows` directory was created along with two YAML files for the pipeline.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372495787/c045503c-3842-4f68-8f90-9ab1bca268dc.png align="center")
    

### **Adding a Virtual Machine as a Runner**

* I'll be using a self-hosted runner to execute the pipeline.
    
* Configure the runner by following the commands provided in GitHub's settings.
    
    ```bash
       Go to "GithubAction_DevOps_Projects"
       Click on settings
       then select the actions and choose "runners"
    ```
    

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372516470/0c919d85-5a85-4929-b714-5d2cd6e136b4.png align="center")
    
* Click on new `self-hosted runner` and select `Linux`
    
* Notedown the token value somewhere as we need to in runner VM.
    

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372560828/758da4cf-b928-486f-a21e-54539da03c7e.png align="center")
    
    Take a putty session of `runner` EC2
    
* Go to `actions-runner` the folder
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372580363/c1423ee3-6a4c-486a-aed6-bd926884bb42.png align="center")
    
* Update/Paste the token value here as mentioned in the screenshot.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372597142/dabb5633-6e66-4d1b-a7b5-cbcf330188a1.png align="center")
    
* Change the execution mode for the script and run it.
    
* `chmod +x` [`selfhost-runner.sh`](http://selfhost-runner.sh)
    

> 💡 **Note:**
> 
> > *Take note of the token value from here and paste it into the script in runner at the following spot. This ensures that the script executes successfully with the necessary permissions. Once you've finished, save your modifications and run the script to test whether it works as planned.*

#### **Troubleshooting:**

* I am getting below error message while executing the file.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372616786/e5389300-7f9c-436f-89c0-58f02f4b24be.png align="center")
    

##### **Fix/Solution:**

* I try explicitly invoking the bash interpreter:
    
    ```Bash
    bash ./selfhost-runner.sh
    ```
    
* The solution is to remove these carriage return characters using the dos2unix command:
    
    1. Install dos2unix if you haven't already:
        
    
    ```Bash
    sudo apt-get update
    sudo apt-get install dos2unix
    ```
    
    2. Run `dos2unix` on [`selfhost-runner.sh`](http://selfhost-runner.sh) script:
        
    
    ```Bash
    dos2unix selfhost-runner.sh
    ```
    
    3. Try running the script again:
        
    
    ```Bash
    ./selfhost-runner.sh
    ```
    

> 💡 **Idea:** This should now execute correctly because the problematic carriage return characters will have been removed

It works :-) And I can execute the file.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372663332/3d97d225-f5f8-44e5-a967-9d25c7ee3999.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372670596/00ba4bc5-2723-49a5-ba03-9bc2fbbf2ec7.png align="center")

### Setup SonarQube

* Go to SonarQube EC2 and notedown the Public IPAddress and open a new browser.
    
* Access SonarQube via `http://<your-server-ip>:9000`.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372710661/7d02214f-5dd5-41c6-858b-7a7c96254ad3.png align="center")
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372717117/5cae1f9b-648b-4fad-ada2-532290c5d4f9.png align="center")
    
    💡 **Note:** *When you access the URL above, you will be prompted to log in. Use "*`admin/admin`" for the first-time login, and you will be asked to change the password. Once you change it, make sure to create a strong and memorable password. After that, you will have full access to the system's features and settings.\_
    

#### Create a token in SonarQube

* Go to `Administration`\&gt;`Security`\&gt;`Users`\&gt;Create a new token
    

![image-1](https://github.com/user-attachments/assets/84265e50-bc10-4959-aee9-36179c2b99ab align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372768298/46387990-53aa-44eb-a70c-d5fd21714800.png align="center")

### Configure Secrets and Variables in GitHub Repo.

```bash
Go to Repo `GithubAction_DevOps_Projects`
Click on `settings`
Click on `Secrets and Variables`
Select `Actions`.
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372783354/6b927cd7-3b65-4952-bdf3-5120691d5b01.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372796151/aa274e1a-9677-42c9-8d83-c0b536a73357.png align="center")

> 💡 **Note:**
> 
> > *You have to update all the required tokens and secrets value here. Part of Terraform code, I have already created a dummy values, which needs to be replaced. Once you have replaced the dummy values with the actual tokens and secrets, ensure that you test the configuration thoroughly to confirm that everything functions as expected. This will help prevent any issues during deployment and maintain the integrity of your infrastructure.*

* **To Update Sonar URL**
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372815770/e892c25e-ef98-451a-b8cf-c2eb6127892a.png align="center")
    
* **To update the** `EKS_KUBECONFIG` secret
    
    * Take a putty session of the Terraform EC2 instance
        
    * Run the command `cat ~/.kube/config`
        
    * Copy the whole content and paste it into the secret.
        

### **Attach Role to Runner EC2**

* Select the EC2 VM and click on the `actions` &gt; `security`\&gt; `Mofify IAM Roles on the runner`.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372843418/65912df5-8d89-4640-9343-85ff915776ee.png align="center")
    
* Select the role `Role_k8_Cluster_profile`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372853273/8d639121-a41c-4e43-90d1-01ae9fee3352.png align="center")
    
* Click on update IAM Role.
    

### **Writing the CI/CD Pipeline**

* **Compile Stage**:
    
    * Use `actions/checkout` to clone the repository.
        
    * Set up the required environment (e.g., JDK 17 for Java projects).
        
    * Compile the application using build tools like Maven.
        
* **Security Checks**:
    
    * Install and run Trivy to scan for vulnerabilities in Docker images.
        
    * Use GitLeaks to detect hardcoded secrets in the source code.
        
* **Unit Testing**:
    
    * Execute test cases to validate the application.
        
* **Build and Publish Docker Image**:
    
    * Build a Docker image using `docker build`.
        
    * Push the image to a container registry or upload it as an artifact.
        
* **Deploy to Kubernetes**:
    
    * Use Terraform to provision an EKS cluster.
        
    * Deploy the application using Kubernetes manifests.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372878284/06b7bd94-8842-443c-bb0a-b171750baae7.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372883237/d0151fdf-fa63-4200-8f51-bc01684f6bab.png align="center")

* Here are the complete [CICD Pipeline details](https://github.com/mrbalraj007/Github-Actions-Project/blob/main/.github/workflows/cicd.yml)
    

### Verify the Docker Image

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372912724/8f380157-aa60-4204-8fac-b8db06575ced.png align="center")

### Verify code coverage in SonarQube

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372923571/53e7fafd-9a66-4eac-9f80-d7c68237df37.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372928069/2ca17e60-2a04-40ba-ba72-bd6bc6608cc4.png align="center")

### Verify pipeline Status

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372948424/bef5fd5b-f6cc-4d3b-bd42-b1d4efd26e71.png align="center")

### Verify the pods in the runner VM

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372962310/ca772f69-5898-41cc-983b-bf4f9cfccae9.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372966636/bab90ca1-6054-4cce-886b-da7a3fa3eb85.png align="center")

### Verify Application Status

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745372988359/75868fb2-4597-4791-8add-2ac5096ee0e4.png align="center")

## Environment Cleanup:

* The following resources were created as part of this project.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373010517/a7f352fb-11d9-476b-b48e-d618da76339a.png align="center")
    
      
    

### To delete a deployment:

* I've created a `Github Action` to destroy the Kubernetes `deployment` and `services`.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373019867/1b0e0f5c-e595-4042-9e82-3babb9d91154.png align="center")
    
* **Delete all deployment/Service**:
    
    * * In GitHub actions, click on the second pipeline to delete the deployment and service.
            
        * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373041520/1f1f47db-58f8-4c25-bdb8-48871772e68c.png align="center")
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373047665/d3ebf565-a4dd-4fb3-b2fe-34480a1c23d3.png align="center")
            
        * Here is the complete [CICD- Pipeline to destroy Deployment and Services](https://github.com/mrbalraj007/Github-Actions-Project/blob/main/.github/workflows/Destroy.yaml)
            

### To delete `AWS EKS cluster`

* Log in to the `Terraform EC2` instance and change the directory to /`k8s_setup_file`, and run the following command to delete the cluster.
    
    * ```sh
        sudo su - ubuntu
        cd /k8s_setup_file
        sudo terraform destroy --auto-approve
        ```
        

#### **Troubleshooting:**

* I am getting below error message while running the `Terraform destroy`.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373061993/d7621270-3f29-4044-8039-38ab024800cf.png align="center")
    

##### **Fix/Solution:**

* I noticed that permission is set to root for terraform dirctory. we have to take ownership first and then try to delete it.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373083401/f4bb5c8c-9062-4005-898d-432719f7500a.png align="center")
    
* Run the following command to take ownership
    
    ```sh
    sudo chown -R ubuntu:ubuntu /home/ubuntu/k8s_setup_file/.terraform*
    ```
    
* I was still getting an error message while running the destroy command.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373097926/1f860866-a200-4605-a231-1403a9480e62.png align="center")
    
* I ran the following command again for the entire Terraform folder.
    
    ```sh
    sudo chown -R ubuntu:ubuntu /home/ubuntu/k8s_setup_file/terraform*
    ```
    
* Rerun the destroy command, and this time it works :-)
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1745373130510/c6ab1299-9b10-4c03-bed3-794fbbbb277d.png align="center")
    

### To delete the `Virtual machine`.

Go to folder *"01.Github\_Action\_DevOps-Project/Terraform\_Code\_Infra\_setup"* and run the terraform command.

* `00.Code_IAC-github-repo`
    
* `01.Code_IAC_Selfhosted-Runner-and-Trivy` - `02.Code_IAC_SonarQube`
    
* `03.Code_IAC_Terraform_box`
    
    ```sh
    Terraform destroy --auto-approve
    ```
    

> 💡 **Note:**
> 
> > You must use this command from `each folder` in order to destroy the entire infrastructure.

---

### **Why Use This Project**

* **Automation**: Reduces manual effort in building, testing, and deploying applications.
    
* **Security**: Ensures code and container security through automated scans.
    
* **Scalability**: Deploys applications to scalable Kubernetes clusters.
    
* **Best Practices**: Demonstrates industry-standard CI/CD practices.
    

---

### **Conclusion**

This project offers a complete guide to setting up a CI/CD pipeline with GitHub Actions. By using tools like Docker, Trivy, SonarQube, and Terraform, it ensures a secure and efficient software delivery process. The use of AWS CLI and Kubernetes shows how to deploy applications to cloud-native environments. This project is a helpful resource for DevOps engineers aiming to implement modern CI/CD pipelines.

**Ref Link:**

* [Youtube VideoLink](https://www.youtube.com/watch?v=icZUzgtz_d8)
    
* [Clearfile-content cache in visualstudio code](https://stackoverflow.com/questions/45216264/clear-file-content-cache-in-visual-studio-code)
    
* [managing-GitHub-access-tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)
    
* [GitHub Actions Markget Place](https://github.com/marketplace)
    
* [download-a-build-artifact](https://github.com/marketplace/actions/download-a-build-artifact)
    
* [upload-a-build-artifact](https://github.com/marketplace/actions/upload-a-build-artifact)