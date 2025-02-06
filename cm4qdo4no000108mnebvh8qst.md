---
title: "Automated AWS DevOps CI/CD Pipeline for Amazon Prime Clone"
datePublished: Mon Dec 16 2024 01:50:20 GMT+0000 (Coordinated Universal Time)
cuid: cm4qdo4no000108mnebvh8qst
slug: automated-aws-devops-cicd-pipeline-for-amazon-prime-clone
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734313562822/1629a957-3a06-4d75-b15d-b58fd0cf86a8.png
tags: docker, sonarqube, kubernetes, npm, terraform, jenkins, helm, prometheus, grafana, eks, ecr, kubernetes-container, argocd, trivy, devsecops-cicd-pipeline-sonarqube-trivy-owasp-jenkins-security-continuousintegration-continuousdeployment-automation-codequality-containersecurity-vulnerabilityscanning-securecoding-softwaredevelopment-devops-cybersecurity-applicationsecurity-softwaresecurity-techblog-itsecurity-codeanalysis-securitytools

---

![Amazon_Prime_Clone project](https://github.com/user-attachments/assets/f8859285-eb80-48df-8279-fc13c59ade0b align="left")

In today's fast-paced tech world, automating application deployment and infrastructure management is essential. This project shows how to set up a complete CI/CD pipeline, use AWS EKS for Kubernetes deployment, and integrate Grafana and Prometheus for monitoring, all using Terraform for infrastructure management. By automating everything, you reduce the need for manual work and enhance the speed and reliability of your deployments..

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

* \[x\] [Clone repository for terraform code](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/19.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * from k8s\_setup\_file/[main.tf](http://main.tf) (i.e `balraj`\*).
        
    * from Virtual machine [main.tf](http://main.tf) (i.e keyname- `MYLABKEY`\*)
        
* \[x\] [App Repo (Simple-DevOps-Project)](https://github.com/mrbalraj007/Amazon-Prime-Clone-Project.git)
    
* \[x\] **AWS Account**: You’ll need an AWS account to create resources like EC2 instances, EKS clusters, and more.
    
* \[x\] **Terraform Knowledge**: Familiarity with Terraform to provision, manage, and clean up infrastructure.
    
* \[x\] **Basic Kubernetes (EKS)**: A basic understanding of Kubernetes, especially Amazon EKS, to deploy and manage containers.
    
* \[x\] **Docker Knowledge**: Basic knowledge of Docker for containerizing applications.
    
* \[x\] **Grafana & Prometheus**: Understanding of these tools to monitor applications and track performance.
    
* \[x\] **Jenkins**: Knowledge of Jenkins for building and automating the CI/CD pipeline.
    
* \[x\] **GitHub**: Experience with GitHub for version control and managing repositories.
    
* \[x\] **Command-Line Tools**: Basic comfort with using the command line for managing infrastructure and services.
    

## Setting Up the Infrastructure

I have created a Terraform code to set up the entire infrastructure, including the installation of required applications, tools, and the EKS cluster automatically created.

**Note** ⇒ `EKS cluster` creation will take approx. 10 to 15 minutes.

* ⇒ EC2 machines will be created named as `"Jenkins-svr"`
    
* ⇒ Jenkins Install
    
* ⇒ Docker Install
    
* ⇒ Trivy Install
    
* ⇒ helm Install
    
* ⇒ Grafan Install using Helm
    
* ⇒ Prometheus Install using Helm
    
* ⇒ AWS Cli Install
    
* ⇒ Terraform Install
    
* ⇒ EKS Cluster Setup
    

### EC2 Instances creation

First, we'll create the necessary virtual machines using `terraform` code.

Below is a terraform Code:

Once you [clone repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git) then go to folder *"19.Real-Time-DevOps-Project/Terraform\_Code/Code\_IAC\_Terraform\_box"* and run the terraform command.

```bash
cd Terraform_Code/Code_IAC_Terraform_box

$ ls -l
dar--l          13/12/24  11:23 AM                All_Pipelines
dar--l          12/12/24   4:38 PM                k8s_setup_file
dar--l          11/12/24   2:48 PM                scripts
-a---l          11/12/24   2:47 PM            507 .gitignore
-a---l          13/12/24   9:00 AM           7238 main.tf
-a---l          11/12/24   2:47 PM           8828 main.txt
-a---l          11/12/24   2:47 PM           1674 MYLABKEY.pem
-a---l          11/12/24   2:47 PM            438 variables.tf
```

**Note** ⇒ Make sure to run [`main.tf`](http://main.tf) from inside the folders.

```bash
19.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box/

dar--l          13/12/24  11:23 AM                All_Pipelines
dar--l          12/12/24   4:38 PM                k8s_setup_file
dar--l          11/12/24   2:48 PM                scripts
-a---l          11/12/24   2:47 PM            507 .gitignore
-a---l          13/12/24   9:00 AM           7238 main.tf
-a---l          11/12/24   2:47 PM           8828 main.txt
-a---l          11/12/24   2:47 PM           1674 MYLABKEY.pem
-a---l          11/12/24   2:47 PM            438 variables.tf
```

You need to run [`main.tf`](http://main.tf) file using the following terraform command.

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

OOnce you run the Terraform command, we will check the following things to ensure everything is set up correctly using Terraform.

### Inspect the `Cloud-Init` logs:

Once connected to EC2 instance then you can check the status of the `user_data` script by inspecting the [log files](https://github.com/mrbalraj007/DevOps_free_Bootcamp/blob/main/19.Real-Time-DevOps-Project/cloud-init-output.log).

```bash
# Primary log file for cloud-init
sudo tail -f /var/log/cloud-init-output.log
                    or 
sudo cat /var/log/cloud-init-output.log | more
```

* *If the user\_data script runs successfully, you will see output logs and any errors encountered during execution.*
    
* *If there’s an error, this log will provide clues about what failed.*
    

Outcome of "`cloud-init-output.log`"

* From Terraform:
    
    ![image-2](https://github.com/user-attachments/assets/a1082c77-1607-4093-b8a7-41c94e358473 align="left")
    

### Verify the Installation

* \[x\] Docker version
    

```bash
ubuntu@ip-172-31-95-197:~$ docker --version
Docker version 24.0.7, build 24.0.7-0ubuntu4.1


docker ps -a
ubuntu@ip-172-31-94-25:~$ docker ps
```

* \[x\] trivy version
    

```bash
ubuntu@ip-172-31-89-97:~$ trivy version
Version: 0.55.2
```

* \[x\] Helm version
    

```bash
ubuntu@ip-172-31-89-97:~$ helm version
version.BuildInfo{Version:"v3.16.1", GitCommit:"5a5449dc42be07001fd5771d56429132984ab3ab", GitTreeState:"clean", GoVersion:"go1.22.7"}
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

* \[x\] aws cli version
    

```bash
ubuntu@ip-172-31-89-97:~$ aws version
usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run:
  aws help
  aws <command> help
  aws <command> <subcommand> help
```

* \[x\] Verify the EKS cluster
    

On the `jenkins` virtual machine, Go to the directory `k8s_setup_file` and open the file `cat apply.log` to verify whether the cluster is created or not.

```sh
ubuntu@ip-172-31-90-126:~/k8s_setup_file$ pwd
/home/ubuntu/k8s_setup_file
ubuntu@ip-172-31-90-126:~/k8s_setup_file$ cd ..
```

After Terraform deploys on the instance, now it's time to set up the cluster. You can SSH into the instance and run:

```bash
aws eks update-kubeconfig --name <cluster-name> --region 
<region>
```

Once the EKS cluster is set up then need to run the following command to make it interact with EKS.

```sh
aws eks update-kubeconfig --name balraj-cluster --region us-east-1
```

*The* `aws eks update-kubeconfig` a command is used to configure your local kubectl tool to interact with an Amazon EKS (Elastic Kubernetes Service) cluster. It updates or creates a kubeconfig file that contains the necessary authentication information to allow kubectl to communicate with your specified EKS cluster.

What happens when you run this command:  
The AWS CLI retrieves the required connection information for the EKS cluster (such as the API server endpoint and certificate) and updates the kubeconfig file located at `~/.kube/config (by default)`. It configures the authentication details needed to connect kubectl to your EKS cluster using IAM roles. After running this command, you will be able to interact with your EKS cluster using kubectl commands, such as `kubectl get nodes` or `kubectl get pods`.

```sh
kubectl get nodes
kubectl cluster-info
kubectl config get-contexts
```

![image-3](https://github.com/user-attachments/assets/4818cf2e-c970-4309-96e3-84d3a7ccd7a7 align="left")

## Setup the Jenkins

Go to Jenkins EC2 and run the following command Access Jenkins via `http://<your-server-ip>:8080`.

* Retrieve the initial admin password using:
    

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![image-4](https://github.com/user-attachments/assets/1fc5315b-0fe1-4a5a-bb7c-fd20645adbb4 align="left")

![image-5](https://github.com/user-attachments/assets/82ae7f28-7cae-476e-99ae-b1d1596c379e align="left")

![image-6](https://github.com/user-attachments/assets/c966a1b3-96a2-4dc6-8329-a3b8f4ee93e3 align="left")

![image-7](https://github.com/user-attachments/assets/867887a1-9d3e-4d22-b37f-b63fac896620 align="left")

![image-8](https://github.com/user-attachments/assets/fc64c254-8d92-4e20-9ce0-76b1938a466a align="left")

### Install a plugin in Jenkins

```sh
Manage Jenkins > Plugins view> Under the Available tab, plugins available for download from the configured Update Center can be searched and considered:
```

Following plugin needs to be installed.

```sh
SonarQube Scanner
NodeJS
Pipeline: Stage View
Blue Ocean
Eclipse Temurin installer
Docker
Docker Commons
Docker Pipeline
Docker API
docker-build-step
Prometheus metrics
```

**Note**⇒ Restart the Jenkins to make it an effective plugin.

#### Create `Webhook` in SonarQube

\- [publicIPaddressofJenkins:9000](publicIPaddressofJenkins:9000)

* Click on
    
    * Administration&gt;Configuration&gt;webooks
        
    * **Name**: sonarqube-webhook
        
    * **URL**: [publicIPaddressofJenkins:8080/sonarqube-webhook](publicIPaddressofJenkins:8080/sonarqube-webhook)
        

![image](https://github.com/user-attachments/assets/58a28ce3-63d1-42ec-8874-9f36f9644626 align="left")

#### Create a token in SonarQube

* Administration&gt;Security&gt;Users&gt;Create a new token
    

![image-1](https://github.com/user-attachments/assets/84265e50-bc10-4959-aee9-36179c2b99ab align="left")

#### Configure Sonarqube credential in Jenkins.

```bash
Dashboard> Manage Jenkins> Credentials> System> Global credentials (unrestricted)
```

![image-2](https://github.com/user-attachments/assets/8006669a-95c0-4d4c-b7cc-1a9cac571b8f align="left")

#### Configure AWS credentials (Access & Secret Keys) in Jenkins

```bash
Dashboard> Manage Jenkins> Credentials> System> Global credentials (unrestricted)
```

![image-3](https://github.com/user-attachments/assets/8c75778c-dd17-493f-a30b-c9159f8bfc51 align="left")

#### Configure/Integrate SonarQube in Jenkins

```bash
Dashboard > Manage Jenkins > System
```

![image-4](https://github.com/user-attachments/assets/16476494-db0c-4874-a2d7-0842194c69de align="left")

![image-5](https://github.com/user-attachments/assets/95aedde7-ac31-46c3-a216-f47d6c3c38a6 align="left")

#### Configure JDK, Sonar scanner, and Node JS

* To configure `JDK`
    

```bash
Dashboard> Manage Jenkins> Tools
```

![image-6](https://github.com/user-attachments/assets/56d3dc2f-2266-4962-a721-d7fae610a4f1 align="left")

![image-7](https://github.com/user-attachments/assets/bc2a089a-8195-47c3-a1f6-a144d90a809b align="left")

* To configure `SonarQube Scanner`
    

```bash
Dashboard > Manage Jenkins > Tools
```

![image-8](https://github.com/user-attachments/assets/8a13ea7c-1abf-44d9-a063-55728ccf0593 align="left")

![image-9](https://github.com/user-attachments/assets/aed870a7-3ffc-4ca2-b519-26073d0cae83 align="left")

* To configure `Node JS`
    

```bash
Dashboard > Manage Jenkins > Tools
```

![image-10](https://github.com/user-attachments/assets/3db3803b-c349-46ec-bcd2-db6e5a17f3c3 align="left")

![image-11](https://github.com/user-attachments/assets/83fe1fef-ea25-48b0-99fb-02d97692e9f8 align="left")

**Note** ⇒ We have to select `NodeJS 16.20.0` as per project required. it won't work on `NodeJs23.x`

* To configure `Docker`
    

```bash
Dashboard > Manage Jenkins > Tools
```

![image-12](https://github.com/user-attachments/assets/47e5d12a-1824-47fa-b31b-eb5479ad24f2 align="left")

#### Build a pipeline.

* Here is the [Pipeline Script](https://github.com/mrbalraj007/DevOps_free_Bootcamp/blob/main/19.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box/All_Pipelines/Pipeline_CI.md)
    
* Build deployment pipeline.
    

Run the pipeline; the first time it would fail, and rerun it with parameters.

* I ran the pipeline but it failed with the below error message.
    

![image-13](https://github.com/user-attachments/assets/3fba5e83-e1e7-4ac3-b34f-673a1993cf29 align="left")

**Solution:**

```sh
sudo su - ansadmin
sudo usermod -aG docker $USER && newgrp docker
sudo usermod -aG docker jenkins && newgrp docker
```

*I ran the pipeline but failed with the same error message. I found the solution below.*

**Solution:**

Jenkins service needs to be restarted.

```bash
sudo systemctl restart jenkins
```

I reran the pipeline, and it went well, and the build was completed successfully.

![image-14](https://github.com/user-attachments/assets/ca86d4ef-46fc-4ba1-a88d-60107e3cac98 align="left")

![image-15](https://github.com/user-attachments/assets/b19d88d8-6d43-4e64-976b-4e96ac8f22f8 align="left")

Build status:

![image-17](https://github.com/user-attachments/assets/edf600a9-5c5e-46af-91f3-e88d49399639 align="left")

![image-18](https://github.com/user-attachments/assets/62642045-c71a-4da7-8c92-60a6affdc629 align="left")

* Application status in SonarQube
    
    ![image-16](https://github.com/user-attachments/assets/c6beb945-6313-48ae-bc2c-ad472d62fe23 align="left")
    

**Quality Gate** Status is failed because of NodeJS mismatch version, as I was using the latest version of Nodes (23.x).

![image-19](https://github.com/user-attachments/assets/9d2b3aab-1517-4a36-a82d-0b08b08bba5f align="left")

* I removed the nodes js 23.x and installed `nodejs16`.
    
* **Note: ⇒ You won't be facing this issue because I have updated the Terraform code.**
    

```sh
sudo apt-get remove -y nodejs

curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Rerun the pipeline and change the quality gate status to passed from failed.

![image-20](https://github.com/user-attachments/assets/02a8781d-b56c-4faa-af35-b409e6052e5b align="left")

```sh
Cleanup Old Images from ECR checks if there are more than 3 images in the repository and deletes the old ones if necessary.
```

![image-21](https://github.com/user-attachments/assets/8f136fc5-ae7f-4f29-8233-7f9486ed2fcb align="left")

![image-22](https://github.com/user-attachments/assets/a9278715-e84b-4320-a03a-9225dcfd15ab align="left")

### Setup ArgoCD

* Run the following commands to verify the `Pods` and `services type`
    

```sh
kubectl get pods -n argocd
kubectl get svc -n argocd

kubectl get pods -n prometheus
kubectl get service -n prometheus
```

```sh
ubuntu@bootstrap-svr:~$ kubectl get pods -n argocd
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          40m
argocd-applicationset-controller-64f6bd6456-79k4l   1/1     Running   0          40m
argocd-dex-server-5fdcd9df8b-85dl7                  1/1     Running   0          40m
argocd-notifications-controller-778495d96f-lsmww    1/1     Running   0          40m
argocd-redis-69fd8bd669-qd4qs                       1/1     Running   0          40m
argocd-repo-server-75567c944-cwrdv                  1/1     Running   0          40m
argocd-server-5c768cdd96-wh4t5                      1/1     Running   0          40m

ubuntu@bootstrap-svr:~$ kubectl get svc -n argocd
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   172.20.37.85     <none>        7000/TCP,8080/TCP            41m
argocd-dex-server                         ClusterIP   172.20.185.246   <none>        5556/TCP,5557/TCP,5558/TCP   41m
argocd-metrics                            ClusterIP   172.20.6.170     <none>        8082/TCP                     41m
argocd-notifications-controller-metrics   ClusterIP   172.20.36.121    <none>        9001/TCP                     41m
argocd-redis                              ClusterIP   172.20.104.129   <none>        6379/TCP                     41m
argocd-repo-server                        ClusterIP   172.20.184.189   <none>        8081/TCP,8084/TCP            41m
argocd-server                             ClusterIP   172.20.150.224   <none>        80/TCP,443/TCP               41m
argocd-server-metrics                     ClusterIP   172.20.208.97    <none>        8083/TCP                     41m
ubuntu@bootstrap-svr:~$
```

```sh
ubuntu@bootstrap-svr:~$ kubectl get pods -n prometheus
NAME                                                     READY   STATUS    RESTARTS   AGE
alertmanager-stable-kube-prometheus-sta-alertmanager-0   2/2     Running   0          42m
prometheus-stable-kube-prometheus-sta-prometheus-0       2/2     Running   0          42m
stable-grafana-6c67f4cb8d-k4bpb                          3/3     Running   0          42m
stable-kube-prometheus-sta-operator-74dcfb4f9c-2vwqr     1/1     Running   0          42m
stable-kube-state-metrics-6d6d5fcb75-w8k4l               1/1     Running   0          42m
stable-prometheus-node-exporter-8tqgh                    1/1     Running   0          42m
stable-prometheus-node-exporter-jkkkf                    1/1     Running   0          42m

ubuntu@bootstrap-svr:~$ kubectl get service -n prometheus
NAME                                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                     ClusterIP   None            <none>        9093/TCP,9094/TCP,9094/UDP   42m
prometheus-operated                       ClusterIP   None            <none>        9090/TCP                     42m
stable-grafana                            ClusterIP   172.20.21.160   <none>        80/TCP                       42m
stable-kube-prometheus-sta-alertmanager   ClusterIP   172.20.20.12    <none>        9093/TCP,8080/TCP            42m
stable-kube-prometheus-sta-operator       ClusterIP   172.20.69.94    <none>        443/TCP                      42m
stable-kube-prometheus-sta-prometheus     ClusterIP   172.20.199.20   <none>        9090/TCP,8080/TCP            42m
stable-kube-state-metrics                 ClusterIP   172.20.52.146   <none>        8080/TCP                     42m
stable-prometheus-node-exporter           ClusterIP   172.20.40.154   <none>        9100/TCP                     42m
```

* Run these commands to change the service type from `ClusterIP` to `LoadBalancer`.
    

```sh
kubectl patch svc stable-kube-prometheus-sta-prometheus -n prometheus -p '{"spec": {"type": "LoadBalancer"}}'
kubectl patch svc stable-grafana -n prometheus -p '{"spec": {"type": "LoadBalancer"}}'
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
```

Verify status now.

![image-24](https://github.com/user-attachments/assets/7d359db7-4768-4a07-9f8d-77c37a2b6df5 align="left")

* Now, time to run the script to get ArgoCD and Grafana access details.
    

[Here is the access script](https://github.com/mrbalraj007/DevOps_free_Bootcamp/blob/main/19.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box/All_Pipelines/access.sh)

Once you access the ArgoCD URL and create an application

* **Application Name**: amazon-prime-app
    
* **Project Name**: default
    
* **Sync Policy**: Automatic (Select Prune Resources and SelfHeal)
    
* **Repository URL**: [https://github.com/mrbalraj007/Amazon-Prime-Clone-Project.git](https://github.com/mrbalraj007/Amazon-Prime-Clone-Project.git)
    
* **Revison**: main
    
* **Path**: k8s\_files (where Kubernetes files reside)
    
* **cluster URL**: Select default cluster
    
* **Namespace**: default
    

![image-25](https://github.com/user-attachments/assets/4caad6f2-dc55-4267-96dc-ea717f9effb8 align="left")

![image-26](https://github.com/user-attachments/assets/a820c29f-8547-4b5e-9186-5e48acdb9d5f align="left")

* Update the **latest image** name in `deployment.yml`
    
    ![image-27](https://github.com/user-attachments/assets/47134897-c0c0-4cda-949b-ef94f38150bd align="left")
    

**Verify the app Status**

![image-28](https://github.com/user-attachments/assets/9c25274d-5464-476e-bfdb-9eddcce21975 align="left")

**Verify Pods & service status**

![image-29](https://github.com/user-attachments/assets/39589d65-4cd4-480c-b5c6-a59fdc341b50 align="left")

Click on the hostnames (URL details) from the service and access it in the browser.

```bash
http://af70e2590416f4788be765b667bb8175-2006799998.us-east-1.elb.amazonaws.com:3000/
```

![image-30](https://github.com/user-attachments/assets/c78e077f-0457-4468-a69a-b71a4a73af2d align="left")

Congratulations :-) the application is working and accessible.

![image-31](https://github.com/user-attachments/assets/70dc9605-5650-43ed-bd06-2c035abcb843 align="left")

* Access Prometheus/Grafana and create a custom dashboard in Prometheus/Grafana.
    

![image-32](https://github.com/user-attachments/assets/452104dd-c859-46cd-909a-c53345055cab align="left")

![image-33](https://github.com/user-attachments/assets/5f96e4af-4b06-49f8-9eae-a22b1c60ba04 align="left")

![image-34](https://github.com/user-attachments/assets/b54fcf5d-98fe-4196-8c6d-d66b154af843 align="left")

![image-35](https://github.com/user-attachments/assets/c0b83f08-4fdc-4f9b-b1cf-8d562918b806 align="left")

Dashboard in Grafana

![image-36](https://github.com/user-attachments/assets/ec873ad5-ed0c-43b4-9d85-2a08e16a5839 align="left")

### Clean up the images and deployment using the pipeline.

* Here is the [Updated pipeline](https://github.com/mrbalraj007/DevOps_free_Bootcamp/blob/main/19.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box/All_Pipelines/Cleanup.md)
    

![image-37](https://github.com/user-attachments/assets/86abb97d-3a99-4ca4-afde-3334ecf4a795 align="left")

*Pipeline would be partially failed because KMS will take some days to get it deleted automatically.*

![image-38](https://github.com/user-attachments/assets/fa32f024-1db8-442e-9fcf-51cecc74d301 align="left")

* Verify the pods and services in EKS. If it is not deleted, then change the service back to ClusterIP and rerun the pipeline.
    

```sh
kubectl patch svc stable-kube-prometheus-sta-prometheus -n prometheus -p '{"spec": {"type": "ClusterIP"}}'
kubectl patch svc stable-grafana -n prometheus -p '{"spec": {"type": "ClusterIP"}}'
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "ClusterIP"}}'
kubectl patch svc singh-app -p '{"spec": {"type": "ClusterIP"}}'
```

## Environment Cleanup:

* As we are using Terraform, we will use the following command to delete
    
* **Delete all deployment/Service** first
    
    * ```sh
          kubectl delete deployment.apps/singh-app
          kubectl delete service singh-app
          kubectl delete service/singh-service
        ```
        
    * `EKS cluster` second
        
    * then delete the `virtual machine`.
        

#### To delete `AWS EKS cluster`

* Log in to the bootstrap EC2 instance, change the directory to `/k8s_setup_file`, and run the following command to delete the cluster.
    

```bash
sudo su - ubuntu
cd /k8s_setup_file
sudo terraform destroy --auto-approve
```

#### Now, time to delete the `Virtual machine`.

Go to folder *"19.Real-Time-DevOps-Project/Terraform\_Code/Code\_IAC\_Terraform\_box"* and run the terraform command.

```bash
cd Terraform_Code/

$ ls -l
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
da---l          26/09/24   9:48 AM                Code_IAC_Terraform_box

Terraform destroy --auto-approve
```

## Conclusion

By combining Terraform, Jenkins, EKS, Docker, and monitoring tools like Grafana and Prometheus, this project automates the entire process of infrastructure management, application deployment, and monitoring. The use of a cleanup pipeline ensures that resources are removed when no longer needed, helping to reduce costs. This approach offers a scalable, efficient, and cost-effective solution for modern application deployment and management.

**Ref Link:**

* [Youtube VideoLink](https://www.youtube.com/watch?v=Gd9Aofx-iLI&t=7808s)
    
* [Install Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)
    
* [Install AWS Cli](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
    
* [EKS Create-repository](https://docs.aws.amazon.com/cli/latest/reference/ecr/create-repository.html)
    
* [EKS describe-repositories](https://docs.aws.amazon.com/cli/latest/reference/ecr/describe-repositories.html)
    
* [AWS configure](https://docs.aws.amazon.com/cli/latest/reference/configure/set.html)
    
* [Jenkins-environment-variables-1](https://phoenixnap.com/kb/jenkins-environment-variables)
    
* [Jenkins-environment-variables-2](https://devopsqa.wordpress.com/2019/11/19/list-of-available-jenkins-environment-variables/)