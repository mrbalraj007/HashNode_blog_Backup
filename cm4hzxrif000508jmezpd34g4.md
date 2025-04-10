---
title: "Seamless Application Deployment to Kubernetes Using a Fully Automated CI/CD Pipeline"
datePublished: Tue Dec 10 2024 05:03:46 GMT+0000 (Coordinated Universal Time)
cuid: cm4hzxrif000508jmezpd34g4
slug: seamless-application-deployment-to-kubernetes-using-a-fully-automated-cicd-pipeline
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1733805683777/ed5efe2e-ca89-4c69-884c-62cafbf4a58e.gif
tags: docker, bootstrap, github, deployment, ansible, kubernetes, maven, git, terraform, jenkins, tomcat, dockerfile, dockerhub, kubernetes-container

---

![Java Based Project](https://github.com/user-attachments/assets/553ad1f6-cf2b-4e1c-a5b1-beaa365f7086 align="left")

This project involves setting up an automated CI/CD pipeline for Kubernetes to deploy a Register App using Jenkins, Ansible, Docker Hub, and Kubernetes. The automation ensures that any change in the GitHub repository triggers the pipeline, which then builds, tests, and deploys the application on Kubernetes.

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

* \[x\] [Clone repository for terraform code](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/17.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * from k8s\_setup\_file/[main.tf](http://main.tf) (i.e `balraj`\*).
        
    * from Virtual machine [main.tf](http://main.tf) (i.e key name- `MYLABKEY`\*)
        
* \[x\] [App Repo (Simple-DevOps-Project)](https://github.com/mrbalraj007/Simple-DevOps-Project)
    
* \[x\] [App Repo (Hello-World)](https://github.com/mrbalraj007/hello-world.git)
    
* \[x\] **Git and GitHub**: You'll need to know the basics of Git for version control and GitHub for managing your repository.
    
* \[x\] **Jenkins Installed**: You need a running Jenkins instance for continuous integration and deployment.
    
* \[x\] **Docker**: Familiarity with Docker is essential for building and managing container images.
    
* \[x\] **Kubernetes (AWS EKS)**: Set up a Kubernetes cluster where you will deploy your applications.
    
* \[x\] **SonarQube**: Installed for code quality checks.
    
* \[x\] **Maven**: Installed for building Java applications.
    

## Step-by-Step Summary

**Kubernetes and Deployment**

* Created Kubernetes deployment and service manifest files for the Register App.
    
* Set the service name as virtual-tech-box-service and port 8080 for the app.
    
* Configured a LoadBalancer service type for public access.
    

**Ansible Configuration for Automation**

* Enabled password-based authentication on the bootstrap server.
    
* Added bootstrap server details in Ansible’s inventory file to allow management.
    
* Configured SSH key-based authentication from the Ansible server to the bootstrap server.
    

**Deployment Using Ansible Playbooks**

* Renamed existing playbooks for clarity, and created a new playbook kube\_deploy.yml for deployment tasks. *Tasks included:*
    
* Deploying the application on Kubernetes.
    
* Creating the service for the app.
    
* Rolling out the deployment if there’s a new Docker image.
    

**Jenkins Configuration for CI/CD**

* CI Job: Configured Jenkins to poll the GitHub repository every minute for changes.
    
* CD Job: Triggered the Ansible playbook on the Ansible server for deployment on Kubernetes.
    
* Configured Docker image creation and push to Docker Hub in the CI job and automated deployment in the CD job.
    

**Testing the Pipeline**

* I made a test commit to the GitHub repository, which automatically triggered the CI/CD pipeline.
    
* Verified deployment by checking the Register App running on the Kubernetes service’s DNS.
    

## Setting Up the Infrastructure

I created a Terraform script to set up the entire infrastructure. This includes the automatic installation of necessary applications, tools, and the EKS cluster..

**<mark>Note</mark>** <mark> ⇒</mark> EKS cluster creation will take approx. 10 to 15 minutes.

* ⇒ EC2 machines will be created named as `"Jenkins-svr", "Tomcat-svr", "bootstrap-svr", "Ansible-svr", "Docker-svr".`
    
* ⇒ Docker Install
    
* ⇒ Ansible Install
    
* ⇒ Tomcat Install
    
* ⇒ Trivy Install
    
* ⇒ EKS Cluster Setup
    

### EC2 Instances creation

First, we'll create the necessary virtual machines using `terraform`.

Below is a terraform Code:

Once you [clone repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git) then go to folder *"17.Real-Time-DevOps-Project/Terraform\_Code/Code\_IAC\_Terraform\_box"* and run the terraform command.

```bash
cd Terraform_Code/Code_IAC_Terraform_box

$ ls -l
da---l          07/10/24   4:43 PM                k8s_setup_file
da---l          07/10/24   4:01 PM                scripts
-a---l          29/09/24  10:44 AM            507 .gitignore
-a---l          09/10/24  10:57 AM           8351 main.tf
-a---l          16/07/21   4:53 PM           1696 MYLABKEY.pem
```

**Note** ⇒ Make sure to run [`main.tf`](http://main.tf) from inside the folders.

```bash
17.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box/

da---l          07/10/24   4:43 PM                k8s_setup_file
da---l          07/10/24   4:01 PM                scripts
-a---l          29/09/24  10:44 AM            507 .gitignore
-a---l          09/10/24  10:57 AM           8351 main.tf
-a---l          16/07/21   4:53 PM           1696 MYLABKEY.pem
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

Once you run the Terraform command, we will check the following things to ensure everything is set up correctly with Terraform.

### Inspect the `Cloud-Init` logs:

Once connected to EC2 instance then you can check the status of the `user_data` script by inspecting the [log files](%5Bcloud-init-output.log%5D\(https://github.com/user-attachments/files/17321314/cloud-init-output.log\)).

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
    
    ![image-1](https://github.com/user-attachments/assets/e3229a10-2c30-4694-ad3b-99ae0e35252d align="left")
    
    ![image-2](https://github.com/user-attachments/assets/a1082c77-1607-4093-b8a7-41c94e358473 align="left")
    

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
    

On the `bootstrap` virtual machine, Go to the directory `k8s_setup_file` and open the file `cat apply.log` to verify whether the cluster is created or not.

```sh
ubuntu@ip-172-31-90-126:~/k8s_setup_file$ pwd
/home/ubuntu/k8s_setup_file
ubuntu@ip-172-31-90-126:~/k8s_setup_file$ cd ..
```

After Terraform deploys on the instance, it's time to set up the cluster. You can SSH into the instance and run:

```bash
aws eks update-kubeconfig --name <cluster-name> --region 
<region>
```

Once the EKS cluster is set up, run the following command to interact with EKS.

```sh
aws eks update-kubeconfig --name balraj-cluster --region us-east-1
```

*The* `aws eks update-kubeconfig` command is used to configure your local kubectl tool to interact with an Amazon EKS (Elastic Kubernetes Service) cluster. It updates or creates a kubeconfig file that contains the necessary authentication information to allow kubectl to communicate with your specified EKS cluster.

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

### Install the plugin in Jenkins

Manage Jenkins &gt; Plugins view&gt; Under the Available tab, plugins available for download from the configured Update Center can be searched and considered:

```sh
Blue Ocean
Pipeline: Stage View
Maven Integration
Pipeline Maven Integration
Eclipse Temurin installer
Docker
Docker Pipeline
Kubernetes
Kubernetes CLI
SonarQube Scanner
```

### Integrate Maven with Jenkins

* Run any job and verify that job is executing successfully.
    
* Create a below pipeline and build it and verify the outcomes.
    
* On Jenkins VM
    

```sh
sudo su - ansadmin
```

* Configure maven and java in Jenkins.  
    
    * Dashboard &gt; Manage Jenkins&gt; Tools
        

![image](https://github.com/user-attachments/assets/69ad49c2-02fc-4ecc-a4e6-4ca0c3fd4779 align="left")

![image-1](https://github.com/user-attachments/assets/bd2b2c73-6f9d-48ad-83b3-8f276f77f28b align="left")

* Build a Test job to verify that the pipeline is working fine.
    

> Name: First-Job  
> Type: Maven Project  
> Git URL: [https://github.com/mrbalraj007/hello-world.git  
> ](https://github.com/mrbalraj007/hello-world.git￼)

![image-2](https://github.com/user-attachments/assets/30ec1e41-0baa-4342-ad8f-70d7fb5d3d14 align="left")

![image-3](https://github.com/user-attachments/assets/d658c3cd-b96e-4494-9b27-2b3216f7c18c align="left")

![image-4](https://github.com/user-attachments/assets/536c5023-e44b-4e8e-9854-2f011e467b4e align="left")

![image-5](https://github.com/user-attachments/assets/830d1c40-5033-4be6-b9c5-7367dde9714f align="left")

### Set Up a Tomcat Server and Deploy Artifacts

Try to open it in browser `<Tomcat EC2 IP Address:8080>`

Click on "Manager App," and you'll see an error message because it's only accessible within the EC2 machine. To allow access from outside, we'll adjust the settings as shown below.

![image-7](https://github.com/user-attachments/assets/615ceab3-bb4a-4001-8fce-b992be39a5b5 align="left")

![image-6](https://github.com/user-attachments/assets/3f0b5e20-edc4-49e4-b585-f9b9470c7410 align="left")

On Tomcat EC2 instance

* Run the following command:
    

```bash
sudo su - ansadmin

ubuntu@tomcat-svr:~$ sudo find / -name context.xml

/opt/tomcat/webapps/docs/META-INF/context.xml
/opt/tomcat/webapps/host-manager/META-INF/context.xml
/opt/tomcat/webapps/manager/META-INF/context.xml
/opt/tomcat/webapps/examples/META-INF/context.xml
/opt/tomcat/conf/context.xml
ubuntu@tomcat-svr:~$
```

* Updating these two files
    

```sh
/opt/tomcat/webapps/host-manager/META-INF/context.xml
/opt/tomcat/webapps/manager/META-INF/context.xml
```

* will take a backup of both files, just in case we need to restore them.
    

```sh
ansadmin@tomcat-svr:~$ sudo cp /opt/tomcat/webapps/host-manager/META-INF/context.xml /opt/tomcat/webapps/host-manager/META-INF/context.xml.bak
ansadmin@tomcat-svr:~$ ls -l /opt/tomcat/webapps/host-manager/META-INF/
total 8
-rwxr-xr-x 1 tomcat tomcat 1351 Feb 27  2023 context.xml
-rwxr-xr-x 1 root   root   1351 Nov  5 01:24 context.xml.bak


ansadmin@tomcat-svr:~$ sudo cp /opt/tomcat/webapps/host-manager/META-INF/context.xml /opt/tomcat/webapps/manager/META-INF/context.xml.bak
ansadmin@tomcat-svr:~$ ls -l /opt/tomcat/webapps/manager/META-INF/
total 8
-rwxr-xr-x 1 tomcat tomcat 1352 Feb 27  2023 context.xml
-rwxr-xr-x 1 root   root   1352 Nov  5 01:25 context.xml.bak
ansadmin@tomcat-svr:~$
```

```bash
cat /opt/tomcat/webapps/host-manager/META-INF/context.xml
```

![image-8](https://github.com/user-attachments/assets/28bed25f-936a-4013-a9ca-fa719528331d align="left")

* You need to comment it out so that it can be accessed externally.
    

```bash
sudo sed -i '/<Valve className="org.apache.catalina.valves.RemoteAddrValve"/ s/^/<!-- /; /allow="127\\\.\\d\+\\.\\d\+\\.\\d+\|::1\|0:0:0:0:0:0:0:1" \/>/ s/$/ -->/' /opt/tomcat/webapps/host-manager/META-INF/context.xml
```

* The requested content should be commented out as shown below.
    

```sh
# sudo vi /opt/tomcat/webapps/host-manager/META-INF/context.xml
  <!--  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
  allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />  -->
```

```bash
cat /opt/tomcat/webapps/manager/META-INF/context.xml
```

![image-9](https://github.com/user-attachments/assets/df43bfbb-0989-4ba7-a99a-0887dba517c0 align="left")

```sh
sudo sed -i '/<Valve className="org.apache.catalina.valves.RemoteAddrValve"/ s/^/<\!-- /; /allow="127\\\.\\d\+\\.\\d\+\\.\\d+\|::1\|0:0:0:0:0:0:0:1" \/>/ s/$/ -->/' /opt/tomcat/webapps/manager/META-INF/context.xml
```

* Requested content would be comment it out as below.
    

```bash
# sudo vi /opt/tomcat/webapps/manager/META-INF/context.xml
  <!--  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
  allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" /> -->
```

now, we will restart the tomcat services.

```bash
sudo systemctl restart tomcat
```

Now, try to access it, and it will ask for a credential to login: `<tomcatIPAddress:8080>`

![image-10](https://github.com/user-attachments/assets/526f26a9-a8cc-4b24-bed5-3ccaa38e0901 align="left")

Now, we need to set up credentials to log in to the Tomcat server. We will search for the `tomcat-users.xml` file on the Tomcat server and append the following:

```bash
ubuntu@tomcat-svr:~$ sudo find / -name tomcat-users.xml
/opt/tomcat/conf/tomcat-users.xml
ubuntu@tomcat-svr:~$
```

* will take a back up of the file
    

```sh
sudo cp /opt/tomcat/conf/tomcat-users.xml /opt/tomcat/conf/tomcat-users.xml.bak

ls -l /opt/tomcat/conf/
-rwxr-xr-x 1 tomcat tomcat   2756 Feb 27  2023 tomcat-users.xml
-rwxr-xr-x 1 root   root     2756 Nov  5 01:42 tomcat-users.xml.bak
```

* add this content as below
    

Update users’ information in the tomcat-users.xml file goto tomcat home directory and Add the below users to the `conf/tomcat-users.xml` file

```bash
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="admin" password="admin" roles="manager-gui, manager-script, manager-jmx, manager-status"/>
<user username="deployer" password="deployer" roles="manager-script"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```

* Command to apply:
    

```sh
sudo sed -i '/<\/tomcat-users>/i \
<role rolename="manager-gui"/>\n\
<role rolename="manager-script"/>\n\
<role rolename="manager-jmx"/>\n\
<role rolename="manager-status"/>\n\
<user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status"/>\n\
<user username="deployer" password="deployer" roles="manager-script"/>\n\
<user username="tomcat" password="s3cret" roles="manager-gui"/>' /opt/tomcat/conf/tomcat-users.xml
```

* To verify the command
    

```sh
cat /opt/tomcat/conf/tomcat-users.xml
```

* Restart the tomcat services.
    

```bash
sudo systemctl restart tomcat
```

* Now, try to reaccess it.
    
    * Credentials would be `admin/admin`
        

![image-11](https://github.com/user-attachments/assets/3394b1bf-befa-44ad-b8b2-e1d9c3d3ec39 align="left")

---

### Integrate Tomcat with Jenkins

* install the following plugin in Jenkins.
    
    * `Deploy to container`
        

![image-12](https://github.com/user-attachments/assets/d50581a4-46c5-401b-903c-0e4f38cffce6 align="left")

* Configure Tomcat server with credentials
    
    > Dashboard&gt; Manage Jenkins&gt; Credentials&gt;System&gt; Global credentials (unrestricted)
    

![image-67](https://github.com/user-attachments/assets/ebb92a04-ab20-4e2a-a059-d244f3ac0bed align="left")

* Build a new pipeline.
    

![image-14](https://github.com/user-attachments/assets/cb4958b5-c41e-4166-bc49-5def5bcaf0a1 align="left")

> name: build code deploy job  
> type: Maven project  
> GIT URL: [https://github.com/mrbalraj007/hello-world.git](https://github.com/mrbalraj007/hello-world.git)

![image-15](https://github.com/user-attachments/assets/3b00fcc2-2071-4143-bdf1-6f582f5063d9 align="left")

![image-16](https://github.com/user-attachments/assets/f6e3d9be-1cff-4276-b7a4-e0ee12836b47 align="left")

Select the `Post-build Actions`

![image-17](https://github.com/user-attachments/assets/a38bc9f7-8eb4-42b1-be57-dfb24f604e69 align="left")

click on `containers` and select `tomcat 9.x remote`

![image-13](https://github.com/user-attachments/assets/ecf67b2e-97ac-47e6-b7f3-71123b373a3d align="left")

* enter the following info
    
    * **WAR/EAR files:** \*\*/\*.war
        
    * **Credentials:** select the credential which we have configured for Tomcat (deployer)
        
    * **Tomcat URL:** &lt;[http://PublicIPAddress](http://PublicIPAddress) of Tomactserver:8080&gt;
        

![image-18](https://github.com/user-attachments/assets/1b92c34a-a9c2-4eac-a084-9228f1774b0d align="left")

* Run the build Job again and validate the artifact on Tomcat server.
    

![image-19](https://github.com/user-attachments/assets/0729d7f3-9746-48b6-b595-5e8c72a62ee9 align="left")

Once you refresh the Tomcat web page and will notice that `webapps` is created and click on it and will see the message.

![image-20](https://github.com/user-attachments/assets/8cba8c3a-94fb-41b4-8b44-bd80df82aca3 align="left")

![image-21](https://github.com/user-attachments/assets/0c89de6b-adeb-4451-9978-fe24eb9ed15f align="left")

Try to modify the code in Git Hub and build it again to double verify that updated content is reflected.

* Need to change this file:
    

![image-22](https://github.com/user-attachments/assets/ef0b0315-d733-4597-be33-80821bc641d3 align="left")

![image-23](https://github.com/user-attachments/assets/e6e14eb3-fcb7-4c94-b693-50a797055826 align="left")

Try to refresh the application: will see the updated content

![image-24](https://github.com/user-attachments/assets/b84a59a7-a73d-43a8-8e36-558e7d548d36 align="left")

Now, we will push the following new code to Tomcat server: [https://www.w3schools.com/howto/howto\_css\_register\_form.asp](https://www.w3schools.com/howto/howto_css_register_form.asp)

* In [Github](https://github.com/mrbalraj007/hello-world.git), Go to the following location, modify the code, and commit it.
    

![image-71](https://github.com/user-attachments/assets/5f62f39d-7a80-4869-b852-3236948990a8 align="left")

![image-25](https://github.com/user-attachments/assets/64f0f4f1-4920-4372-ba74-7ebd02651217 align="left")

Congratulations :-) The updated code works.

### Setup a Docker Server

* Create a Docker container (Tomcat)
    
* On Docker System
    
* Pull tomcat image
    

```sh
sudo su - ansadmin
docker pull tomcat
```

```sh
ansadmin@docker-svr:~$ docker pull tomcat
Using default tag: latest
latest: Pulling from library/tomcat
ff65ddf9395b: Pull complete
1fffcd81c86b: Pull complete
23441222f0af: Pull complete
9ad70f2fec4f: Pull complete
eb1faaa4cea3: Pull complete
64473b717cff: Pull complete
4f4fb700ef54: Pull complete
ed0603f1436c: Pull complete
Digest: sha256:7e26fc3555d46ac4446763f182af86287308d6251b234b5a3428d255dbc59582
Status: Downloaded newer image for tomcat:latest
docker.io/library/tomcat:latest

ansadmin@docker-svr:~$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
tomcat       latest    228690642041   3 weeks ago   470MB
```

* Create a container from the tomcat image and verify it.
    

```sh
docker run -d --name tomcat-container -p 8081:8000 tomcat
```

```sh
ansadmin@docker-svr:~$ docker run -d --name tomcat-container -p 8081:8000 tomcat
65281ab56991e1bb7909719009c7fd5b9112d38ac9493d8ff677381272624460

ansadmin@docker-svr:~$ docker ps -la
CONTAINER ID   IMAGE     COMMAND             CREATED          STATUS          PORTS                                                   NAMES
65281ab56991   tomcat    "catalina.sh run"   22 seconds ago   Up 22 seconds   8080/tcp, 0.0.0.0:8081->8000/tcp, [::]:8081->8000/tcp   tomcat-container
```

try to access it on the browser

![image-26](https://github.com/user-attachments/assets/ea07d72f-19b8-4804-a849-ee54226be511 align="left")

we are unable to access it because we have to log into the container and move the directory from `webapp.dist` to `webapps`.

* login into the container
    

```sh
docker container exec -it tomcat-container /bin/bash
```

```sh
ansadmin@docker-svr:~$ docker container exec -it tomcat-container /bin/bash

root@4969c2d31a08:/usr/local/tomcat# ls

bin  BUILDING.txt  conf  CONTRIBUTING.md  lib  LICENSE  logs  native-jni-lib  NOTICE  README.md  RELEASE-NOTES  RUNNING.txt  temp  webapps  webapps.dist  work

root@4969c2d31a08:/usr/local/tomcat# cd webapps
root@4969c2d31a08:/usr/local/tomcat/webapps# ls

root@4969c2d31a08:/usr/local/tomcat/webapps# cd ../webapps.dist/

root@4969c2d31a08:/usr/local/tomcat/webapps.dist# ls
docs  examples  host-manager  manager  ROOT

root@4969c2d31a08:/usr/local/tomcat/webapps.dist# cp -R * ../webapps

root@4969c2d31a08:/usr/local/tomcat/webapps.dist# cd ../..

root@4969c2d31a08:/usr/local# ls
bin  etc  games  include  lib  man  sbin  share  src  tomcat

root@4969c2d31a08:/usr/local# cd tomcat/

root@4969c2d31a08:/usr/local/tomcat# ls
bin  BUILDING.txt  conf  CONTRIBUTING.md  lib  LICENSE  logs  native-jni-lib  NOTICE  README.md  RELEASE-NOTES  RUNNING.txt  temp  webapps  webapps.dist  work

root@4969c2d31a08:/usr/local/tomcat# cd webapps

root@4969c2d31a08:/usr/local/tomcat/webapps# ls
docs  examples  host-manager  manager  ROOT
root@4969c2d31a08:/usr/local/tomcat/webapps#

exit
```

* Create Centos custom image from the docker file.
    

```sh
cat Dockerfile
-------------------------------------------

FROM openjdk:11-jre-slim

# Create Tomcat directory
RUN mkdir /opt/tomcat

# Set working directory
WORKDIR /opt/tomcat

# Download and extract Tomcat
ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.97/bin/apache-tomcat-9.0.97.tar.gz .

# Extract Tomcat and move files
RUN tar -xvzf apache-tomcat-9.0.97.tar.gz && \
    mv apache-tomcat-9.0.97/* . && \
    rm -rf apache-tomcat-9.0.97 apache-tomcat-9.0.97.tar.gz

# Expose the Tomcat port
EXPOSE 8080

# Set the command to run Tomcat
CMD ["/opt/tomcat/bin/catalina.sh", "run"]
```

* ***Error*** :
    
    * *I encountered the error message below and found that the Tomcat minor version had changed. If you are facing the same issue, you can use this URL to download the latest version of Tomcat.*
        
    * URL: [https://dlcdn.apache.org/tomcat](https://dlcdn.apache.org/tomcat)
        

```sh
  ansadmin@docker-svr:~$ docker build -t mytomcat .
[+] Building 0.2s (5/9)                                                                                                                docker:default
 => [internal] load build definition from Dockerfile                                                                                             0.0s
 => => transferring dockerfile: 567B                                                                                                             0.0s
 => [internal] load metadata for docker.io/library/openjdk:11-jre-slim                                                                           0.1s
 => [internal] load .dockerignore                                                                                                                0.0s
 => => transferring context: 2B                                                                                                                  0.0s
 => [1/5] FROM docker.io/library/openjdk:11-jre-slim@sha256:93af7df2308c5141a751c4830e6b6c5717db102b3b31f012ea29d842dc4f2b02                     0.0s
 => => resolve docker.io/library/openjdk:11-jre-slim@sha256:93af7df2308c5141a751c4830e6b6c5717db102b3b31f012ea29d842dc4f2b02                     0.0s
 => ERROR [4/5] ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.96/bin/apache-tomcat-9.0.96.tar.gz .                                           0.0s
------
 > [4/5] ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.96/bin/apache-tomcat-9.0.96.tar.gz .:
------
ERROR: failed to solve: failed to load cache key: invalid response status 404
```

* Build the image
    

```sh
docker build -t mytomcat .
```

```sh
ansadmin@docker-svr:~$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
mytomcat     latest    43d8de92038e   19 seconds ago   254MB
tomcat       latest    228690642041   3 weeks ago      470MB
alpine       latest    91ef0af61f39   8 weeks ago      7.8MB
```

* ***Troubleshooting***:
    
    * Check Network Connectivity: Ensure that your Docker daemon has internet access. You can check this by running a simple container and trying to ping an external website:
        
        ```bash
        docker run --rm alpine ping -c 4 google.com
        ```
        

```sh
ansadmin@docker-svr:~$ docker run -d --name mytomcat-server -p 8083:8000 mytomcat
0eeaf0b738e14b3cab0ac81311d78992cc849b5e5022c0f8080bfe7f8d4d0e45
ansadmin@docker-svr:~$ docker ps -a
CONTAINER ID   IMAGE      COMMAND                  CREATED         STATUS         PORTS                                                   NAMES
0eeaf0b738e1   mytomcat   "/opt/tomcat/bin/cat…"   6 seconds ago   Up 5 seconds   8080/tcp, 0.0.0.0:8083->8000/tcp, [::]:8083->8000/tcp   mytomcat-server
ansadmin@docker-svr:~$
```

* try to access it in the browser
    

```sh
http://dockerpublicIPddress:8083/
```

You will still get a same error message

![image-72](https://github.com/user-attachments/assets/3b232c22-5529-41cd-a339-f0bd77227e70 align="left")

* Clean all images and containers.
    

```sh
docker stop $(docker ps -aq) && docker rm $(docker ps -aq) && docker rmi $(docker images -q) && docker volume prune -f && docker network prune -f
```

* Now, we will create a new image and container
    

```sh
cat Dockerfile
-----------------------------------------
FROM tomcat:latest

# Create Tomcat directory
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
```

* Build the image
    

```sh
docker build -t mytomcat .
```

* Create a container
    

```sh
docker run -d --name mytomcat-server -p 8083:8080 mytomcat
```

* Try to access it one more time, and you will see the Tomcat web page is accessible.
    

![image-27](https://github.com/user-attachments/assets/ad51e9d6-2ed4-488f-aac5-5ca9d7e8f05f align="left")

### Integrated Docker with Jenkins

* Will install `publish over ssh` a plugin in Jenkins.
    
* **On Jenkins Server**
    

```sh
sudo su - ansadmin

ansadmin@Jenkins-svr:~$ ls -la
total 24
drwxr-x--- 3 ansadmin ansadmin 4096 Nov  5 01:04 .
drwxr-xr-x 4 root     root     4096 Nov  5 01:04 ..
-rw-r--r-- 1 ansadmin ansadmin  220 Mar 31  2024 .bash_logout
-rw-r--r-- 1 ansadmin ansadmin 3771 Mar 31  2024 .bashrc
-rw-r--r-- 1 ansadmin ansadmin  807 Mar 31  2024 .profile
drwx------ 2 ansadmin ansadmin 4096 Nov  5 01:04 .ssh
```

```sh
cat ~/.ssh/id_rsa.pub
```

* **Passwordless authentication from** `Jenkins` to `Docker`.
    
    * On docker-machine
        

```sh
sudo ls -la /home/ansadmin/.ssh/authorized_keys 
sudo touch /home/ansadmin/.ssh/authorized_keys
sudo ls -la /home/ansadmin/.ssh/authorized_keys
sudo chmod 600 /home/ansadmin/.ssh/authorized_keys
```

* Change the ownership from `root` to `ansadmin`
    

```sh
ansadmin@docker-svr:~$ ls -la .ssh/
total 16
drwx------ 2 ansadmin ansadmin 4096 Nov  5 02:50 .
drwxr-x--- 4 ansadmin ansadmin 4096 Nov  5 02:33 ..
-rwx------ 1 root     root        0 Nov  5 02:50 authorized_keys
-rw------- 1 ansadmin ansadmin 1831 Nov  5 01:05 id_rsa
-rw-r--r-- 1 ansadmin ansadmin  401 Nov  5 01:05 id_rsa.pub
```

```sh
sudo chown ansadmin:ansadmin /home/ansadmin/.ssh/authorized_keys
```

```sh
ansadmin@docker-svr:~$ sudo chown ansadmin:ansadmin /home/ansadmin/.ssh/authorized_keys

ansadmin@docker-svr:~$ ls -la .ssh/
total 16
drwx------ 2 ansadmin ansadmin 4096 Nov  5 02:50 .
drwxr-x--- 4 ansadmin ansadmin 4096 Nov  5 02:33 ..
-rw------- 1 ansadmin ansadmin    0 Nov  5 02:50 authorized_keys
-rw------- 1 ansadmin ansadmin 1831 Nov  5 01:05 id_rsa
-rw-r--r-- 1 ansadmin ansadmin  401 Nov  5 01:05 id_rsa.pub
```

* Share the public key from `Jenkins` to `Docker`
    
    * from Jenkins Server
        
        ```sh
        cat ~/.ssh/id_rsa.pub
        ```
        

On Docker Server

```sh
sudo echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZRbhOo4JbH+8/51dYlD/XkOj2aYG7XxRUHSf9wZSF84KIW0SnBLroOq4P2+mm2vkeN6k5CUEvvFUAdL1/6sgFkPGGrvRgDCNPXFVwfERuogUK9pqCSHbf0bV/3TOTWyhR9Frgjl8EGUU/qysxD0to0f5KoeK5ON0aA1m1/qzZw/Bz+LTt1NuZHyvccODsppGYCzFTxezwygypxuF0EWAnyBvA+blLozif6GOzNp6OyF0xriPhIDd8+mNaFSiP7AVAMPtUIMCQAkRv1nGk9faKiUZWBwpsykjqoS2Ox0UYChRSsbs9VRXQre/+mo+8xOP5UaykpGPx3TD+8+uClon/ ansadmin@Jenkins-svr" >> /home/ansadmin/.ssh/authorized_keys
```

* Validate access
    

Do the following testing from the Jenkins Server

```sh
ssh ansadmin@dockerIP address

or

ssh 172.31.21.246 # if both EC2 instance are in same subnet/VPC
```

![image-73](https://github.com/user-attachments/assets/c0cc2e27-bfb3-4f5f-ab66-ef265e133c5f align="left")

* Configure docker into Jenkins  
    
    Dashboard&gt; Manage Jenkins&gt; System
    

![image-28](https://github.com/user-attachments/assets/eebee1f0-fff2-4acd-913a-02c8161269d8 align="left")

![image-74](https://github.com/user-attachments/assets/3f8751bf-687a-427e-aa5b-93d165791933 align="left")

* Validate the connection.
    
    ![image-29](https://github.com/user-attachments/assets/8516d3c7-69c2-46b4-bdbf-5f314947f469 align="left")
    

---

### Build and Copy artifacts to Docker container

* On docker server
    

> We have to create a folder "`docker-artifact`" inside the `/opt` folder and move Dockerfile in it and give permission accordingly.

```bash
sudo su - ansadmin
dockeradmin@docker-svr:~$ cd /opt
dockeradmin@docker-svr:/opt$ pwd
/opt

dockeradmin@docker-svr:/opt$ sudo mkdir docker-artifact
dockeradmin@docker-svr:/opt$ ls -l
total 8

drwx--x--x 4 root root 4096 Oct 25 11:10 containerd
drwxr-xr-x 2 root root 4096 Oct 25 12:33 docker-artifact
dockeradmin@docker-svr:/opt$ cd

dockeradmin@docker-svr:/opt$ sudo mv /home/ansadmin/Dockerfile /opt/docker-artifact
dockeradmin@docker-svr:/opt$ cd docker-artifact/
dockeradmin@docker-svr:/opt/docker-artifact$ ls -l
total 4
-rw-rw-r-- 1 ansadmin ansadmin 115 Oct 25 11:35 Dockerfile
```

Now, we have to change the ownership from root to dockeradmin

```sh
dockeradmin@docker-svr:/opt$ sudo chown -R ansadmin:ansadmin docker-artifact/

ansadmin@docker-svr:/opt$ ls -la
total 16
drwxr-xr-x  4 root     root     4096 Nov  5 03:11 .
drwxr-xr-x 22 root     root     4096 Nov  5 01:04 ..
drwx--x--x  4 root     root     4096 Nov  5 01:05 containerd
drwxr-xr-x  2 ansadmin ansadmin 4096 Nov  5 03:11 docker-artifact
```

* **Append the existing pipeline for maven based**  
    build name: build code deploy job  
    type: Maven:  
    GIT URL: [https://github.com/mrbalraj007/hello-world.git](https://github.com/mrbalraj007/hello-world.git)
    

![image-30](https://github.com/user-attachments/assets/ef50b96f-a8f8-4d35-8c74-87215abfe8d1 align="left")

![image-31](https://github.com/user-attachments/assets/67551682-0bbd-4c26-8038-59dac513cafa align="left")

![image-32](https://github.com/user-attachments/assets/14249b64-fddc-4b1b-adb8-28bb2f93458d align="left")

Append the pipeline and give the remote directory path.

> Source file: **webapp/target/\*.war**  
> Remove Prefix: **webapp/target**  
> Remote Directory: **//opt//docker-artifact**

![image-35](https://github.com/user-attachments/assets/995c0540-528f-47bc-a86b-d7cac7afbc2e align="left")

* Run the pipeline again and validate the `warfile` on docker-machine as below
    

![image-36](https://github.com/user-attachments/assets/124ff893-a3be-4f3e-89f4-2da8dfa6613b align="left")

![image-34](https://github.com/user-attachments/assets/40748ab4-7ea8-4e78-b27c-47592554af9f align="left")

![image-33](https://github.com/user-attachments/assets/f78139d5-e1e1-4b22-bc13-3c0d4e545cbc align="left")

* Now, update the Dockerfile, so that Artificat can be copied to a new container.
    

On Docker EC2 Instance

```sh
ansadmin@docker-svr:/opt/docker-artifact$ pwd
/opt/docker-artifact

cat Dockerfile
------------------------------------------------
FROM tomcat:latest

# Create Tomcat directory
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps

# copy artifact
COPY ./*.war /usr/local/tomcat/webapps
```

### Automate build and deployment on the docker container.

Append the pipeline `Build Code Deploy Job` and add the following in `Exec command` the pipeline.

```sh
cd /opt/docker-artifact
docker build -t regapp:v1 .;
docker stop mytomcat-server;
docker rm mytomcat-server;
docker run -d --name mytomcat-server -p 8087:8080 regapp:v1
```

![image-75](https://github.com/user-attachments/assets/ab6d87c2-3167-48f0-b340-6f225bbc7f92 align="left")

Note:If the build fails, rerun the pipeline, and it should execute successfully.

![image-76](https://github.com/user-attachments/assets/a85f3240-3cfe-4e86-b141-63965a0aa07a align="left")

Results:

![image-37](https://github.com/user-attachments/assets/c480c8a1-a962-4db2-b61a-5985ee413ca8 align="left")

### Ansible Server Setup and Create a container using Ansible.

* On the ansible node
    

> **Task**  
> add to hosts file  
> copy ssh keys to Docker server  
> test the connection

---

1. **Add hosts in the inventory file**.  
    Will create an entry in the inventory
    
2. add docker host IP address in the inventory
    

Note- Make sure you log in to both servers using the same user, `ansadmin`, from the `ansible` server to the `docker` servers.

*File location*: cat /etc/ansible/hosts

```sh
localhost
# [ansible]
# 172.31.30.244  # Private IP address of ansible serve
[docker]
172.31.21.246  # Private IP address of Docker server
```

1. Copy `SSH key` from `Ansible` to `Docker` Server
    

```sh
sudo su - ansadmin
cat .ssh/id_rsa.pub
```

* **On docker-machine**
    

````sh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDY1IuK7HKJF3P9Bc/jBUhKomZNpLc+tVqNhdkQMQr/8JglV+ECzMKsxLM/xL7kegWCIOLp9Tt2PTMK3nxRBAKz3Bqi/UwzmWamTXNCkmI6mJVHqHprcqdNScdrm4NeZ1Z2Q/OI4JXOeIJQjguPsWelGmQvvl2T5+MWlUErv1jvFgR9hDBIHf5bED5e/dGko9spSu5RCFSCIGNnBYgYEnYrCcywKHrtuFk8FWQKv1nan0RNCp0vDV7mFl0iUToyx90NrSFEi3QcjicC/880y+SRAKK8UJ+nnq2vI5yyxsg2otjNDtcbvqDQ0rzLp029cps3pYd+p/JF+H0NYb08XHUd ansadmin@ansible-svr" >> /home/ansadmin/.ssh/authorized_keys

- Verify access from Ansible Server.
```sh
ssh ansadmin@private IP address 

or

ssh private IP address  # if both instances are in same vpc.
````

![image-77](https://github.com/user-attachments/assets/d0167f7e-c7b1-496c-9583-98bf7fd2dc53 align="left")

2. Copy `SSH key` to [Localhost](http://Localhost) (Ansible)
    

```sh
sudo su - ansadmin
cat .ssh/id_rsa.pub
```

* On Ansible (local) machine
    

````sh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDY1IuK7HKJF3P9Bc/jBUhKomZNpLc+tVqNhdkQMQr/8JglV+ECzMKsxLM/xL7kegWCIOLp9Tt2PTMK3nxRBAKz3Bqi/UwzmWamTXNCkmI6mJVHqHprcqdNScdrm4NeZ1Z2Q/OI4JXOeIJQjguPsWelGmQvvl2T5+MWlUErv1jvFgR9hDBIHf5bED5e/dGko9spSu5RCFSCIGNnBYgYEnYrCcywKHrtuFk8FWQKv1nan0RNCp0vDV7mFl0iUToyx90NrSFEi3QcjicC/880y+SRAKK8UJ+nnq2vI5yyxsg2otjNDtcbvqDQ0rzLp029cps3pYd+p/JF+H0NYb08XHUd ansadmin@ansible-svr" >> /home/ansadmin/.ssh/authorized_keys

- Verify access
```sh
ssh localhost
````

![image-80](https://github.com/user-attachments/assets/abebfe9f-c327-4fe1-94c5-81e9d61692b9 align="left")

* Run the Ansible ADHOC-command
    

```sh
ansible all -m command -a uptime
```

![image-38](https://github.com/user-attachments/assets/6d6cc98d-a9f0-46b4-b240-13e0650fbaea align="left")

### Integrate Ansible with Jenkins

> Task
> 
> 1. share the SSH key To Ansible server from Jenkins.
>     

* On Ansible Server.
    

```sh
sudo su - ansadmin

sudo ls -la /home/ansadmin/.ssh/authorized_keys 
sudo touch /home/ansadmin/.ssh/authorized_keys
sudo ls -la /home/ansadmin/.ssh/authorized_keys
sudo chmod 600 /home/ansadmin/.ssh/authorized_keys
```

* Change the ownership from root to `ansadmin`
    

```sh
ansadmin@docker-svr:~$ ls -la .ssh/
total 16
drwx------ 2 ansadmin ansadmin 4096 Nov  5 02:50 .
drwxr-x--- 4 ansadmin ansadmin 4096 Nov  5 02:33 ..
-rwx------ 1 root     root        0 Nov  5 02:50 authorized_keys
-rw------- 1 ansadmin ansadmin 1831 Nov  5 01:05 id_rsa
-rw-r--r-- 1 ansadmin ansadmin  401 Nov  5 01:05 id_rsa.pub
```

```sh
sudo chown ansadmin:ansadmin /home/ansadmin/.ssh/authorized_keys
```

```sh
ansadmin@docker-svr:~$ sudo chown ansadmin:ansadmin /home/ansadmin/.ssh/authorized_keys

ansadmin@docker-svr:~$ ls -la .ssh/
total 16
drwx------ 2 ansadmin ansadmin 4096 Nov  5 02:50 .
drwxr-x--- 4 ansadmin ansadmin 4096 Nov  5 02:33 ..
-rw------- 1 ansadmin ansadmin    0 Nov  5 02:50 authorized_keys
-rw------- 1 ansadmin ansadmin 1831 Nov  5 01:05 id_rsa
-rw-r--r-- 1 ansadmin ansadmin  401 Nov  5 01:05 id_rsa.pub
```

* Share the public key from `Jenkins` to `Ansible`
    
* On Jenkins Server
    

```sh
ansadmin@Jenkins-svr:~$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZRbhOo4JbH+8/51dYlD/XkOj2aYG7XxRUHSf9wZSF84KIW0SnBLroOq4P2+mm2vkeN6k5CUEvvFUAdL1/6sgFkPGGrvRgDCNPXFVwfERuogUK9pqCSHbf0bV/3TOTWyhR9Frgjl8EGUU/qysxD0to0f5KoeK5ON0aA1m1/qzZw/Bz+LTt1NuZHyvccODsppGYCzFTxezwygypxuF0EWAnyBvA+blLozif6GOzNp6OyF0xriPhIDd8+mNaFSiP7AVAMPtUIMCQAkRv1nGk9faKiUZWBwpsykjqoS2Ox0UYChRSsbs9VRXQre/+mo+8xOP5UaykpGPx3TD+8+uClon/ ansadmin@Jenkins-svr
```

* On Ansible Server.
    

```sh
sudo echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZRbhOo4JbH+8/51dYlD/XkOj2aYG7XxRUHSf9wZSF84KIW0SnBLroOq4P2+mm2vkeN6k5CUEvvFUAdL1/6sgFkPGGrvRgDCNPXFVwfERuogUK9pqCSHbf0bV/3TOTWyhR9Frgjl8EGUU/qysxD0to0f5KoeK5ON0aA1m1/qzZw/Bz+LTt1NuZHyvccODsppGYCzFTxezwygypxuF0EWAnyBvA+blLozif6GOzNp6OyF0xriPhIDd8+mNaFSiP7AVAMPtUIMCQAkRv1nGk9faKiUZWBwpsykjqoS2Ox0UYChRSsbs9VRXQre/+mo+8xOP5UaykpGPx3TD+8+uClon/ ansadmin@Jenkins-svr" >> /home/ansadmin/.ssh/authorized_keys
```

* Verify access from Jenkins Server.
    

```sh
ssh privateIPAddress of Ansible server.
```

![image-78](https://github.com/user-attachments/assets/9b450dca-7201-470f-b216-aaf6d03f695c align="left")

### Configure Ansible in Jenkins

> Dashboard&gt; Manage Jenkins&gt; System

click on `Test configuration`\&gt; Save.

* On Ansible Server
    

Create a folder named "`docker-artifact`" inside the `/opt` directory and grant user permissions.

```bash
ansadmin@ansible-svr:$ cd /opt
ansadmin@ansible-svr:/opt$ sudo mkdir -p docker-artifact
ansadmin@ansible-svr:/opt$ ls -la -la
total 12
drwxr-xr-x  3 root root 4096 Oct 28 05:29 .
drwxr-xr-x 22 root root 4096 Oct 28 03:54 ..
drwxr-xr-x  2 root root 4096 Oct 28 05:29 docker-artifact

ansadmin@ansible-svr:/opt$ sudo chown ansadmin:ansadmin docker-artifact
ansadmin@ansible-svr:/opt$ ls -l
total 4
drwxr-xr-x 2 ansadmin ansadmin 4096 Oct 28 05:29 docker-artifact
ansadmin@ansible-svr:/opt$
```

Append the existing pipeline `Build Code Deploy Job` and modify as below in the **transfer set**

![image-83](https://github.com/user-attachments/assets/f719457c-4417-452e-b115-ed50d6ec4f88 align="left")

![image-41](https://github.com/user-attachments/assets/59def552-ccc2-4405-8163-1211657d0e17 align="left")

* run the pipeline and validate the `warfile` is copied or not.
    
* from Ansible
    

![image-42](https://github.com/user-attachments/assets/5f44e7b5-ca22-4778-819a-9cbfe1559952 align="left")

**I can see artifacts copied from** `Jenkins` to `Ansible servers`.

* build an image and create a container on Ansible.
    

We have a Dockerfile located in `/opt/docker-artifact/Dockerfile` on the Docker server. We will use this Dockerfile to build a Docker image directly on the Ansible server

We have a Dockerfile in `/opt/docker-artifact` on the Ansible server, and we will use it to build an image directly on the Ansible server.

```bash
 cat Dockerfile
 ------------------------------------------
FROM tomcat:latest

# Create Tomcat directory
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps

# copy artifact
COPY ./*.war /usr/local/tomcat/webapps
```

Need to run the following command

```sh
cd /opt/docker-artifact
docker build -t regapp:v1 .
docker run -t --name regapp-server -p 8081:8080 regapp:v1
```

Try to access it in the browser

* Public IP address of the Ansible server:8081
    
* Cleanup the docker image and container
    
    * On Ansible Server
        
        ```sh
        docker stop $(docker ps -aq) && docker rm $(docker ps -aq) && docker rmi $(docker images -q) && docker volume prune -f && docker network prune -f
        ```
        

* Write a playbook on Ansible server.
    

> uncomment ansible entry from `/etc/ansible/hosts` file

```sh
localhost
# [ansible]
# 172.31.30.244  # Private IP address of ansible serve
[docker]
172.31.89.173   # Private IP address of Docker server
```

Go to directory **/opt/docker-artifact**

Add the following play. vi task.yml

```sh

---
- hosts: ansible
  tasks:
    - name: create a docker image
      command: docker build -t regapp:latest .
      args:
        chdir: /opt/docker-artifact
```

```sh
ansible-playbook task.yml -C  # to DRY run

ansible-playbook task.yml
```

* Outcomes
    

```sh
ansadmin@ansible-svr:/opt/docker-artifact$ ansible-playbook task.yml

PLAY [ansible] ******************************************************************************************************************************************************

TASK [Gathering Facts] **********************************************************************************************************************************************
[WARNING]: Platform linux on host 172.31.30.244 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python
interpreter could change the meaning of that path. See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more
information.
ok: [172.31.30.244]

TASK [create a docker image] ****************************************************************************************************************************************
changed: [172.31.30.244]

PLAY RECAP **********************************************************************************************************************************************************
172.31.30.244              : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


ansadmin@ansible-svr:/opt/docker-artifact$ docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
regapp       latest    8dbb1aafd145   13 seconds ago   476MB
tomcat       latest    228690642041   3 weeks ago      470MB
```

![image-46](https://github.com/user-attachments/assets/91c9aee9-3729-4dff-a0b5-c40bd2c2c196 align="left")

* Copy the image to Dockerhub
    
    * first, we need to login into `dockerhub` from `ansible server`
        
        ```sh
        docker login -u <dockerhubvlogin ID>
        ```
        

![image-47](https://github.com/user-attachments/assets/6dff8bd4-ad0c-4054-8984-8dccde702778 align="left")

Add to the playbook to include tags and push to Docker Hub, then update the playbook.

Note--. you must have login with ansadmin otherwise, you may not able to push the image to dockerhub.

vi task.yml

```sh
---
- hosts: ansible
  tasks:
    - name: create a docker image
      command: docker build -t regapp:latest .
      args:
        chdir: /opt/docker-artifact

    - name: create tag tpush image onto dockerhub
      command: docker tag regapp:latest balrajsi/regapp:latest

    - name: push docker image
      command: docker push balrajsi/regapp:latest
```

```sh
ansible-playbook task.yml
               or
ansible-playbook task.yml --limit 172.31.22.134
```

![image-48](https://github.com/user-attachments/assets/2b71cf7d-c68e-4fdc-9917-4612b7b962ab align="left")

![image-49](https://github.com/user-attachments/assets/062066c1-b3d8-49e7-87ca-3fae621f2b8a align="left")

### To automate the ansible playbook in Jenkins.

Append the existing pipeline and add the following line in pipeline.

![image-50](https://github.com/user-attachments/assets/a97b5b30-6d93-40bd-9b66-789245543970 align="left")

I run the pipeline and it is showing unstable.

![image-52](https://github.com/user-attachments/assets/ccbb467c-39dc-4356-a6e5-4aaa42bfd271 align="left")

I noticed that I need to specify the Ansible server and the full path of the playbook, then run the pipeline again.

![image-51](https://github.com/user-attachments/assets/0404ae28-e486-4bc9-81e8-5b8267b85c13 align="left")

![image-53](https://github.com/user-attachments/assets/8f70abab-90b3-4b71-81e5-fd159f5e2e85 align="left")

* Now, we will add a task to create a container and pull the image from Docker Hub..
    

> *Will pull image from Docker hub and will create a container on Docker server*

* I have created a new playbook called container.yml.
    

On Ansible Server

```sh
 cat container.yml
---
- hosts: docker

  tasks:
    - name: create container
      command: docker run -d --name regapp-server -p 8082:8080 balrajsi/regapp:latest
```

```sh
ansible-playbook container.yml -C

ansible-playbook container.yml
```

I browse it and the apps is accessible.

![image-54](https://github.com/user-attachments/assets/1325dac8-0ee2-4a6b-a1ee-fd104c1ed9d0 align="left")

If you rerun the playbook, it will fail with an error saying that the container already exists.

![image-55](https://github.com/user-attachments/assets/04167de9-f637-4f79-b1c6-a6c6ce39fc65 align="left")

* We need to update the playbook to adjust the following requirement
    

> task
> 
> 1. remove the existing container
>     
> 2. remove existing image
>     
> 3. create a new container
>     

```sh
 cat container.yml
---
- hosts: docker
  tasks:
    - name: Stop existing container if running
      command: docker stop regapp-server
      ignore_errors: yes

    - name: Remove the container if it exists
      command: docker rm regapp-server
      ignore_errors: yes

    - name: Remove all images for balrajsi/regapp
      shell: docker images -q balrajsi/regapp | xargs -r docker rmi -f
      ignore_errors: yes

    - name: Pull the latest image
      command: docker pull balrajsi/regapp:latest

    - name: Create and start the container
      command: docker run -d --name regapp-server -p 8082:8080 balrajsi/regapp:latest
```

> The shell module is used with the command docker images -q balrajsi/regapp | xargs -r docker rmi -f to:  
> 
> List all image IDs related to balrajsi/regapp.  
> 
> Use xargs to delete each of these images, with -f to force the deletion.
> 
> This will remove all images, including those with tags for balrajsi/regapp.

![image-56](https://github.com/user-attachments/assets/b55e4e68-bd2f-4430-8394-460ac8b5ef51 align="left")

![image-57](https://github.com/user-attachments/assets/03281e9e-059d-42ab-bb0f-6262a38232a5 align="left")

* ### <mark>Let's </mark> `Automate` <mark> the pipeline</mark>.
    

Now, add the following in `exec command`\-

```sh
ansible-playbook /opt/docker-artifact/task.yml;
sleep 10;
ansible-playbook /opt/docker-artifact/container.yml
```

![image-59](https://github.com/user-attachments/assets/05164682-1887-430e-99b8-3fd6fa321a00 align="left")

![image-58](https://github.com/user-attachments/assets/4e2cb644-551f-47a7-adab-e9561036f409 align="left")

* Outcomes:
    
    ![image-82](https://github.com/user-attachments/assets/5243ab62-c885-4704-ba8b-1dd5f3bf0f1e align="left")
    

> public IP address of Docker EC2 instance :8082 # for Tomcat public IP address of Docker EC2 instance :8082/webapp # for application access

[http://54.208.37.114:8082/](http://54.208.37.114:8082/)

[http://54.208.37.114:8082/webapp](http://54.208.37.114:8082/webapp)

![image-84](https://github.com/user-attachments/assets/bf8d92b0-2171-4613-adf9-afc708a60e40 align="left")

![image-85](https://github.com/user-attachments/assets/5e82c485-36b9-4278-ad85-b947f0d21fc5 align="left")

We have deployed the container successfully, but still it has a problem.

**Problem Statement**: Every time the container is terminated or stopped, the image is deleted, and there is no high availability for the application.

**Solution:**

* Container Management System: Configure it for high availability and fault tolerance using Docker Swarm or Kubernetes.
    

### **Setup EKS cluster**

After Terraform deploys on the instance, now it's time to setup the cluster. You can SSH into the instance "bootstrap-svr" and run:

```bash
aws eks update-kubeconfig --name <cluster-name> --region 
<region>
```

Once the EKS cluster is set up, you need to run the following command to interact with EKS.

```sh
sudo su - ansadmin

aws eks update-kubeconfig --name balraj-cluster --region us-east-1
```

The `aws eks update-kubeconfig` command is used to configure your local kubectl tool to interact with an Amazon EKS (Elastic Kubernetes Service) cluster. It updates or creates a kubeconfig file that contains the necessary authentication information to allow kubectl to communicate with your specified EKS cluster.

What happens when you run this command:  
The AWS CLI retrieves the required connection information for the EKS cluster (such as the API server endpoint and certificate) and updates the kubeconfig file located at ~/.kube/config (by default). It configures the authentication details needed to connect kubectl to your EKS cluster using IAM roles. After running this command, you will be able to interact with your EKS cluster using kubectl commands, such as `kubectl get nodes` or `kubectl get pods`.

```sh
kubectl get nodes
kubectl cluster-info
kubectl config get-contexts
```

![image-60](https://github.com/user-attachments/assets/a9a7b98d-ad50-404f-b2e7-453555d042b3 align="left")

![image-61](https://github.com/user-attachments/assets/40c4a5b3-9940-498e-8a12-e7ad32f96624 align="left")

```sh
**Repo URL:**
https://github.com/mrbalraj007/Simple-DevOps-Project/blob/master/Kubernetes/regapp-deploy.yml
https://github.com/mrbalraj007/Simple-DevOps-Project/blob/master/Kubernetes/regapp-service.yml
```

```sh
 cat regapp-deploy.yml
 --------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: singh-regapp
  labels:
     app: regapp

spec:
  replicas: 2
  selector:
    matchLabels:
      app: regapp

  template:
    metadata:
      labels:
        app: regapp
    spec:
      containers:
      - name: regapp
        image: balrajsi/regapp
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
```

```sh
ubuntu@ip-172-31-34-2:~$ kubectl get all
NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   7m31s

ubuntu@ip-172-31-34-2:~$ kubectl get nodes
NAME                         STATUS   ROLES    AGE     VERSION
ip-10-0-1-35.ec2.internal    Ready    <none>   4m46s   v1.31.0-eks-a737599
ip-10-0-2-111.ec2.internal   Ready    <none>   4m49s   v1.31.0-eks-a737599
ip-10-0-2-200.ec2.internal   Ready    <none>   4m50s   v1.31.0-eks-a737599
ubuntu@ip-172-31-34-2:~$ kubectl cluster-info
Kubernetes control plane is running at https://E19F4781A98D52717F1E7711CE499650.gr7.us-east-1.eks.amazonaws.com
CoreDNS is running at https://E19F4781A98D52717F1E7711CE499650.gr7.us-east-1.eks.amazonaws.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
ubuntu@ip-172-31-34-2:~$ ls
k8s_setup_file
ubuntu@ip-172-31-34-2:~$ vi regapp-deploy.yml
ubuntu@ip-172-31-34-2:~$ kubectl apply -f regapp-deploy.yml
deployment.apps/singh-regapp created

ubuntu@ip-172-31-34-2:~$ kubectl get all
NAME                               READY   STATUS              RESTARTS   AGE
pod/singh-regapp-f6787dd68-9p8lq   1/1     Running             0          17s
pod/singh-regapp-f6787dd68-hlkzl   0/1     ContainerCreating   0          17s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   172.20.0.1   <none>        443/TCP   14m

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/singh-regapp   1/2     2            1           17s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/singh-regapp-f6787dd68   2         2         1       17s

ubuntu@ip-172-31-34-2:~$ kubectl get pod -o wide
NAME                           READY   STATUS    RESTARTS   AGE   IP           NODE                         NOMINATED NODE   READINESS GATES
singh-regapp-f6787dd68-9p8lq   1/1     Running   0          48s   10.0.1.252   ip-10-0-1-35.ec2.internal    <none>           <none>
singh-regapp-f6787dd68-hlkzl   1/1     Running   0          48s   10.0.2.248   ip-10-0-2-200.ec2.internal   <none>           <none>
ubuntu@ip-172-31-34-2:~$
```

* To create a service
    

```sh
ubuntu@ip-172-31-34-2:~$ cat regapp-service.yml
apiVersion: v1
kind: Service
metadata:
  name: singh-service
  labels:
    app: regapp
spec:
  selector:
    app: regapp

  ports:
    - port: 8080
      targetPort: 8080

  type: LoadBalancer
```

```sh
ubuntu@ip-172-31-34-2:~$ ls -l
total 12
drwxr-xr-x 6 ubuntu ubuntu 4096 Oct 30 10:13 k8s_setup_file
-rw-rw-r-- 1 ubuntu ubuntu  481 Oct 30 10:21 regapp-deploy.yml
-rw-rw-r-- 1 ubuntu ubuntu  195 Oct 30 10:22 regapp-service.yml
ubuntu@ip-172-31-34-2:~$ kubectl apply -f regapp-service.yml
service/singh-service created

ubuntu@ip-172-31-34-2:~$ kubectl get all
NAME                               READY   STATUS    RESTARTS   AGE
pod/singh-regapp-f6787dd68-9p8lq   1/1     Running   0          2m42s
pod/singh-regapp-f6787dd68-hlkzl   1/1     Running   0          2m42s

NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP                                                              PORT(S)          AGE
service/kubernetes      ClusterIP      172.20.0.1       <none>                                                                   443/TCP          16m
service/singh-service   LoadBalancer   172.20.182.136   abaa5583d8ba54f7f8f66242e89fd100-336899443.us-east-1.elb.amazonaws.com   8080:32600/TCP   23s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/singh-regapp   2/2     2            2           2m42s

NAME                                     DESIRED   CURRENT   READY   AGE
replicaset.apps/singh-regapp-f6787dd68   2         2         2       2m42s
ubuntu@ip-172-31-34-2:~$
```

* To verify the service
    
    ```sh
    kubectl describe service/singh-service
    ```
    

```sh
kubectl describe service/singh-service
-----------------------------------------------------------
Name:                     singh-service
Namespace:                default
Labels:                   app=regapp
Annotations:              <none>
Selector:                 app=regapp
Type:                     LoadBalancer
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       172.20.182.136
IPs:                      172.20.182.136
LoadBalancer Ingress:     abaa5583d8ba54f7f8f66242e89fd100-336899443.us-east-1.elb.amazonaws.com
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  32600/TCP
Endpoints:                10.0.2.248:8080,10.0.1.252:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Internal Traffic Policy:  Cluster
Events:
  Type    Reason                Age    From                Message
  ----    ------                ----   ----                -------
  Normal  EnsuringLoadBalancer  2m17s  service-controller  Ensuring load balancer
  Normal  EnsuredLoadBalancer   2m14s  service-controller  Ensured load balancer
```

Try to access application in the browser

```sh
abaa5583d8ba54f7f8f66242e89fd100-336899443.us-east-1.elb.amazonaws.com:8080/webapp
```

![image-62](https://github.com/user-attachments/assets/53902701-20f3-41d6-84c6-8cd27a4da7cc align="left")

* **Integrate Kubernetes (Bootstrap Server) with Ansible.**
    
    * On bootstrap server, Verify the following things.
        
        * ansadmin exists
            
        * add ansadmin to sudoers files
            
        * Enable password based login
            
    * On ansible Node
        
        * Add to hosts file (Bootstrap IP address) need to add in inventory file
            
        * copy ssh keys
            
        * test the connection.
            

login on to ansible server with \`\`\`ansadmin\`\`\`\`

```sh
ubuntu@ansible-svr:~$ sudo su - ansadmin
ansadmin@ansible-svr:~$
```

* Rename the file name to `docker_deployment.yml` on directory docker-artifact/
    

```sh
ansadmin@ansible-svr:/opt/docker-artifact$ ls -la
total 24
drwxr-xr-x 2 ansadmin ansadmin 4096 Oct 30 10:39 .
drwxr-xr-x 4 root     root     4096 Oct 28 05:39 ..
-rw-rw-r-- 1 ansadmin ansadmin  172 Oct 28 05:40 Dockerfile
-rw-r--r-- 1 ansadmin ansadmin  450 Oct 30 10:38 container.yml
-rw-rw-r-- 1 ansadmin ansadmin  354 Oct 30 10:39 task.yml
-rw-rw-r-- 1 ansadmin ansadmin 2246 Oct 29 01:52 webapp.war
ansadmin@ansible-svr:/opt/docker-artifact$

nsadmin@ansible-svr:/opt/docker-artifact$ mv container.yml docker_deployment.yml
ansadmin@ansible-svr:/opt/docker-artifact$ ls -la
total 24
drwxr-xr-x 2 ansadmin ansadmin 4096 Oct 30 10:41 .
drwxr-xr-x 4 root     root     4096 Oct 28 05:39 ..
-rw-rw-r-- 1 ansadmin ansadmin  172 Oct 28 05:40 Dockerfile
-rw-r--r-- 1 ansadmin ansadmin  450 Oct 30 10:38 docker_deployment.yml
-rw-rw-r-- 1 ansadmin ansadmin  354 Oct 30 10:39 task.yml
-rw-rw-r-- 1 ansadmin ansadmin 2246 Oct 29 01:52 webapp.war
ansadmin@ansible-svr:/opt/docker-artifact$ cat task.yml
---
- hosts: ansible
  tasks:
    - name: create a docker image
      command: docker build -t regapp:latest .
      args:
        chdir: /opt/docker-artifact

    - name: create tag push image onto dockerhub
      command: docker tag regapp:latest balrajsi/regapp:latest

    - name: push docker image
      command: docker push balrajsi/regapp:latest
ansadmin@ansible-svr:/opt/docker-artifact$
```

* Get the public key which will be required to in next step.
    
    ```sh
    cat ~/.ssh/id_rsa.pub
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDY1IuK7HKJF3P9Bc/jBUhKomZNpLc+tVqNhdkQMQr/8JglV+ECzMKsxLM/xL7kegWCIOLp9Tt2PTMK3nxRBAKz3Bqi/UwzmWamTXNCkmI6mJVHqHprcqdNScdrm4NeZ1Z2Q/OI4JXOeIJQjguPsWelGmQvvl2T5+MWlUErv1jvFgR9hDBIHf5bED5e/dGko9spSu5RCFSCIGNnBYgYEnYrCcywKHrtuFk8FWQKv1nan0RNCp0vDV7mFl0iUToyx90NrSFEi3QcjicC/880y+SRAKK8UJ+nnq2vI5yyxsg2otjNDtcbvqDQ0rzLp029cps3pYd+p/JF+H0NYb08XHUd ansadmin@ansible-svr
    ```
    
* On Kubernetes machine login with ansadmin and run the following command
    
    ```sh
    mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys && echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDY1IuK7HKJF3P9Bc/jBUhKomZNpLc+tVqNhdkQMQr/8JglV+ECzMKsxLM/xL7kegWCIOLp9Tt2PTMK3nxRBAKz3Bqi/UwzmWamTXNCkmI6mJVHqHprcqdNScdrm4NeZ1Z2Q/OI4JXOeIJQjguPsWelGmQvvl2T5+MWlUErv1jvFgR9hDBIHf5bED5e/dGko9spSu5RCFSCIGNnBYgYEnYrCcywKHrtuFk8FWQKv1nan0RNCp0vDV7mFl0iUToyx90NrSFEi3QcjicC/880y+SRAKK8UJ+nnq2vI5yyxsg2otjNDtcbvqDQ0rzLp029cps3pYd+p/JF+H0NYb08XHUd ansadmin@ansible-svr" >> ~/.ssh/authorized_keys chmod 600 ~/.ssh/authorized_keys
    ```
    
* Verify access from `Ansible` to `bootstrap server`.
    
    * from ansible server
        
        ```sh
        ssh 172.31.81.82
        ```
        

![image-86](https://github.com/user-attachments/assets/99275e64-5bda-4a34-a660-5e0c6d3b840c align="left")

* Will update `kubernets private IP address` in host file (`/etc/ansible/hosts`) as below
    

![image-87](https://github.com/user-attachments/assets/2d11035a-5879-417d-8508-459ce9d65568 align="left")

* Try to ping all servers from Ansible server.
    

```sh
ansible all -m ping
[WARNING]: Platform linux on host 172.31.34.2 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
172.31.34.2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.12"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host 172.31.22.134 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
172.31.22.134 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.12"
    },
    "changed": false,
    "ping": "pong"
}
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
localhost | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3.12"
    },
    "changed": false,
    "ping": "pong"
}
172.31.20.86 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 172.31.20.86 port 22: No route to host",
    "unreachable": true
}
ansadmin@ansible-svr:/opt/docker-artifact$

------------------------------------------------------------------
+ ansible all -a uptime

[WARNING]: Platform linux on host 172.31.34.2 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
172.31.34.2 | CHANGED | rc=0 >>
 10:55:48 up 55 min,  5 users,  load average: 0.34, 0.36, 0.31
[WARNING]: Platform linux on host 172.31.22.134 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that
path. See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
172.31.22.134 | CHANGED | rc=0 >>
 10:55:49 up 23 min,  4 users,  load average: 0.06, 0.03, 0.01
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
localhost | CHANGED | rc=0 >>
 10:55:49 up 23 min,  4 users,  load average: 0.06, 0.03, 0.01
172.31.20.86 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 172.31.20.86 port 22: No route to host",
    "unreachable": true
}
```

* Delete the existing services/deployment on Kubernetes if running any.
    
    * From `Bootstap Server`
        
        ```sh
        ansadmin@ip-172-31-34-2:~$ kubectl delete -f regapp-service.yml  -f regapp-deploy.yml
        E1030 11:05:22.346941   24345 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"http://localhost:8080/api?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused"
        E1030 11:05:22.349374   24345 memcache.go:265] "Unhandled Error" err="couldn't get current server API group list: Get \"http://localhost:8080/api?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused"
        unable to recognize "regapp-service.yml": Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
        unable to recognize "regapp-deploy.yml": Get "http://localhost:8080/api?timeout=32s": dial tcp 127.0.0.1:8080: connect: connection refused
        ansadmin@ip-172-31-34-2:~$ exit
        logout
        ubuntu@ip-172-31-34-2:~$ kubectl delete -f regapp-service.yml  -f regapp-deploy.yml
        service "singh-service" deleted
        deployment.apps "singh-regapp" deleted
        ubuntu@ip-172-31-34-2:~$
        ```
        
* **Deploy** `deployment and service` using ansible.
    
* From Ansible server
    

```sh
ubuntu@ansible-svr:/opt/docker-artifact$ cat kube_deploy.yml
---
- hosts: kubernetes
  tasks:
    - name: deploy regapp on kubernetes
      command: kubectl apply -f /home/ansadmin/regapp-deploy.yml  # we need to defile fine complete path of the file which running on kuberners

ubuntu@ansible-svr:/opt/docker-artifact$ cat kube_service.yml
---
- hosts: kubernetes
  tasks:
    - name: deploy regapp on kubernets
      command: kubectl apply -f /home/ansadmin/regapp-service.yml # we need to define the complete path of the file.
ubuntu@ansible-svr:/opt/docker-artifact$
```

* Run the playbook
    
    ```sh
    cd /opt/docker-artifact/
    ansible-playbook kube_deploy.yml
    ```
    

```sh
ansible-playbook kube_deploy.yml
PLAY [kubernetes] ********************************************************************************************************************************************************************************************
TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 172.31.34.2 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
ok: [172.31.34.2]

TASK [deploy regapp on kubernetes] ***************************************************************************************************************************************************************************
fatal: [172.31.34.2]: FAILED! => {"changed": true, "cmd": ["kubectl", "apply", "-f", "/home/ansadmin/regapp-deploy.yml"], "delta": "0:00:00.094222", "end": "2024-10-30 11:08:46.366405", "msg": "non-zero return code", "rc": 1, "start": "2024-10-30 11:08:46.272183", "stderr": "error: error validating \"/home/ansadmin/regapp-deploy.yml\": error validating data: failed to download openapi: Get \"http://localhost:8080/openapi/v2?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused; if you choose to ignore these errors, turn validation off with --validate=false", "stderr_lines": ["error: error validating \"/home/ansadmin/regapp-deploy.yml\": error validating data: failed to download openapi: Get \"http://localhost:8080/openapi/v2?timeout=32s\": dial tcp 127.0.0.1:8080: connect: connection refused; if you choose to ignore these errors, turn validation off with --validate=false"], "stdout": "", "stdout_lines": []}

PLAY RECAP ***************************************************************************************************************************************************************************************************
172.31.34.2                : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```

I encountered an error, and here is the solution.

**Solution**: Since I was using ansadmin, we need to grant permission.

```sh
ansadmin@ip-172-31-34-2:~$ aws eks update-kubeconfig --name balraj-cluster --region us-east-1

Added new context arn:aws:eks:us-east-1:373160674113:cluster/balraj-cluster to /home/ansadmin/.kube/config
```

I run the playbook again

```sh
ansadmin@ansible-svr:/opt/docker-artifact$ ansible-playbook kube_deploy.yml

PLAY [kubernetes] ********************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 172.31.34.2 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
ok: [172.31.34.2]

TASK [deploy regapp on kubernetes] ***************************************************************************************************************************************************************************
changed: [172.31.34.2]

PLAY RECAP ***************************************************************************************************************************************************************************************************
172.31.34.2                : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

* view from Kubernetes servers
    
* now, run `service` playbook on ansible server.
    

```sh
ansadmin@ansible-svr:/opt/docker-artifact$ ansible-playbook kube_service.yml

PLAY [kubernetes] ********************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host 172.31.34.2 is using the discovered Python interpreter at /usr/bin/python3.12, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.17/reference_appendices/interpreter_discovery.html for more information.
ok: [172.31.34.2]

TASK [deploy regapp on kubernets] ****************************************************************************************************************************************************************************
changed: [172.31.34.2]

PLAY RECAP ***************************************************************************************************************************************************************************************************
172.31.34.2                : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

![image-64](https://github.com/user-attachments/assets/7327ad8f-40c9-4a47-9ef8-652f28d400fc align="left")

```sh
http://a86cae209e1e345c09fc9bfa1fc6738c-890978031.us-east-1.elb.amazonaws.com:8080/webapp/
```

Hurray :-) Application is accessible now.

![image-65](https://github.com/user-attachments/assets/4d447971-49e3-4f6c-8a17-6f55e3880396 align="left")

* Delete all existing services and deployment from `bootstrap server`.
    

```sh
ansadmin@bootstrap-svr:~$ kubectl delete service/singh-service
service "singh-service" deleted

ansadmin@bootstrap-svr:~$ kubectl delete deployment.apps/singh-regapp
deployment.apps "singh-regapp" deleted
```

* **Create Jenkins Deployment Job for Kubernetes**
    
    * build a new pipeline as below
        
    * name: RegApp\_CD\_Job Type: freestyle project choose ":send build artifacts over ssh" from post-build actions. Below thing need to update in pipeline.
        

```sh
ansible-playbook /opt/docker-artifact/kube_deploy.yml;
ansible-playbook /opt/docker-artifact/kube_service.yml
```

![image-66](https://github.com/user-attachments/assets/39bde0c9-ebc9-493c-921a-7bf8ebc92925 align="left")

* Run the pipeline
    
* *The CD job is now working as expected. Next, we will set up a complete CI/CD pipeline to fully automate the process*.
    
* will create a new build pipeline as below
    

```sh
Name: RegApp_CI_Job
Copyfrom: Build Code Deploy Job
```

don't forget to update the file name in `exec command` as we have renamed it as below.

```sh
cd /opt/docker-artifact;
ansible-playbook task.yml;
sleep 10;
ansible-playbook docker_deployment.yml   # file name earlier was container.
```

![image-89](https://github.com/user-attachments/assets/c65028fc-c0f9-484c-84ef-02a8137f4d47 align="left")

* Run the pipeline and you will notice it's completed successfully.
    
* Now, we will configure CD job on CI task.
    
    * Select the pipeline "`RegApp_CI_Job`" and click on "`Add Post-build action` and choose "`build other projects`"
        

![image-91](https://github.com/user-attachments/assets/ecf948ed-72aa-4497-a379-782e59be4b50 align="left")

and select Pipeline as `RegApp_CD_Job`

![image-92](https://github.com/user-attachments/assets/0fd12c2e-9c12-4f25-8a95-d767d96b4b8e align="left")

* Run the pipeline `RegApp_CI_Job`
    
* Update the playbook as below and try to rerun it.
    

```sh
nsadmin@ansible-svr:/opt/docker-artifact$ cat kube_service.yml
---
- hosts: kubernetes
  tasks:
    - name: deploy regapp on kubernets
      command: kubectl apply -f /home/ansadmin/regapp-service.yml # we need to define the complete path of the file.

    # the following play in your playbook  
    - name: update deployment with new pods if image updated in docker hub
      command: kubectl rollout restart deployment.apps/singh-regapp
ansadmin@ansible-svr:/opt/docker-artifact$
```

* State from K8s
    
    ![image-93](https://github.com/user-attachments/assets/7013fa13-fb5d-4fd6-8eea-ffc744745a2e align="left")
    

Application is accessible and working fine :-)

![image-94](https://github.com/user-attachments/assets/263a1faf-cc5e-47f7-9a66-899a8d69c195 align="left")

### Resources used in AWS:

* EC2 instances
    

![image-95](https://github.com/user-attachments/assets/e3eb8f01-9069-4d6f-a39d-a470792db0d3 align="left")

* EKS Cluster
    
    ![image-65](https://github.com/user-attachments/assets/b325403d-40c5-46a8-aa5b-f96b8c626f20 align="left")
    

## Environment Cleanup:

* As we are using Terraform, we will use the following command to delete
    
* **Delete all deployment/Service** first
    
    * ```sh
          kubectl delete service/singh-service
          kubectl delete deployment.apps/singh-regapp
        ```
        
    
    ![image-70](https://github.com/user-attachments/assets/5bce49b5-6443-42d5-9f63-695af1b664e0 align="left")
    
    * `EKS cluster` second
        
    * then delete the `virtual machine`.
        

#### To delete `AWS EKS cluster`

* Login into the bootstrap EC2 instance and change the directory to /k8s\_setup\_file, and run the following command to delete the cluste.
    

```bash
sudo su - ubuntu
cd /k8s_setup_file
sudo terraform destroy --auto-approve
```

![image-68](https://github.com/user-attachments/assets/e4c6c1e4-235d-40e7-b8e2-ea15f180d9ab align="left")

#### Now, time to delete the `Virtual machine`.

Go to folder *"17.Real-Time-DevOps-Project/Terraform\_Code/Code\_IAC\_Terraform\_box"* and run the terraform command.

```bash
cd Terraform_Code/

$ ls -l
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
da---l          26/09/24   9:48 AM                Code_IAC_Terraform_box

Terraform destroy --auto-approve
```

![image-69](https://github.com/user-attachments/assets/e88d2e01-005a-4078-8602-6cd9e15e5877 align="left")

## Conclusion

References For a deeper understanding and detailed steps on similar setups, feel free to check the following technical blogs:

**Ref Link**

* [YouTube Link](https://www.youtube.com/watch?v=5_s7EmZWz78)
    
* [Docker Image Module](https://docs.ansible.com/ansible/2.8/modules/docker_image_module.html)
    
* [Kubernetes Setup](https://kubernetes.io/docs/setup/)
    
* [Minimum IAM policies](https://eksctl.io/usage/minimum-iam-policies/#)
    
* [Amazon EKS identity-based policy examples](https://docs.aws.amazon.com/eks/latest/userguide/security-iam-id-based-policy-examples.html)
    
* [Set up kubectl and eksctl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
    
* [Maven installation](https://maven.apache.org/install.html)
    
* [Maven LifeCycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html)