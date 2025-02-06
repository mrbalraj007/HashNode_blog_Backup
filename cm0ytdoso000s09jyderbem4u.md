---
title: "Creating a Starbucks Clone on AWS: A Comprehensive DevSecOps Guide"
datePublished: Thu Sep 12 2024 04:53:27 GMT+0000 (Coordinated Universal Time)
cuid: cm0ytdoso000s09jyderbem4u
slug: creating-a-starbucks-clone-on-aws-a-comprehensive-devsecops-guide
tags: owasp, docker, github, sonarqube, git, containers, terraform, jenkins, gmail, docker-image, cloudwatch, jenkins-devops, trivy, jenkins-pipeline, docker-scout

---

In this blog, we’ll guide you through deploying a Starbucks clone on AWS using a DevSecOps approach. This method combines development, security, and operations practices to ensure a smooth and secure deployment. We’ll cover the key steps, the technologies used, and how to troubleshoot common issues.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116636908/8bc144b9-c68a-4f5c-99b0-2bc91f869f0f.png align="center")

## Prerequisites for This Project

Before you start, ensure you have the following:

* [Terraform Code](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/11.Real-Time-DevOps-Project/Terraform_Code)
    
* [App Code Repo](https://github.com/mrbalraj007/starbucks.git)
    
* AWS Account: Set up an account to manage resources and services.
    
* Jenkins Installed: Ensure Jenkins is configured and running for CI/CD processes.
    
* Docker: Install Docker and Docker Scout for building and scanning Docker images.
    
* AWS Services: Configure AWS CloudWatch for monitoring and SNS for notifications.
    
* SonarQube: Install and configure SonarQube for code quality analysis.
    
* OWASP Dependency-Check: Install for vulnerability scanning of project dependencies.
    

## Technologies Used:

* **Jenkins**: Automation server for building, testing, and deploying code.
    
* **Docker**: Container platform for building and running applications.
    
* **Docker Hub**: Repository for storing and sharing Docker images.
    
* **CloudWatch**: AWS service for monitoring and managing cloud resources.
    
* **SNS (Simple Notification Service)**: AWS service for sending notifications.
    
* **SonarQube**: Code quality and security analysis tool.
    
* **OWASP Dependency-Check**: Tool for identifying vulnerabilities in project dependencies.
    
* [**Docker Scout**](https://earthly.dev/blog/docker-scout/,): Tool for scanning Docker images for security vulnerabilities.
    

## Setting Up the Environment

I have created a Terraform file to set up the entire environment, including the installation of required applications, tools, and the EKS cluster automatically created.

**Note**⇒ I was using `t3.medium` and having performance issues; I was unable to run the pipeline, and it got stuck in between. So, I am now using `t2.xlarge` now. Also, you have to update `your email address` in the `main.tf` file so that topic for alerting can be created while creation the VM.

#### Setting Up the Virtual Machines (EC2)

First, we'll create the necessary virtual machines using `terraform`.

Below is a terraform configuration:

Once you [clone repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git) then go to folder *"11.Real-Time-DevOps-Project/Terraform\_Code"* and run the terraform command.

```bash
cd Terraform_Code/

$ ls -l
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
da---l          10/09/24   7:32 PM                Terraform_Code
```

**Note** ⇒ Make sure to run `main.tf` from inside the folders.

```bash
cd 11.Real-Time-DevOps-Project/Terraform_Code"

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---l          21/08/24   2:56 PM            500 .gitignore
-a---l          10/09/24   7:29 PM           4287 main.tf
-a---l          10/09/24   1:17 PM           3379 terrabox_install.sh
```

You need to run the `main.tf` file using the following Terraform command.

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply --auto-approve
```

---

### **Environment Setup**

| HostName | OS |
| --- | --- |
| Jenkins | Ubuntu 24 LTS |

> * Password for the **root** account on all these virtual machines is **xxxxxxx**
>     
> * Perform all the commands as root user unless otherwise specified
>     

Once you run the terraform command, we will verify the following things to ensure everything is set up via terraform.

#### Verify the Jenkins version

```bash
# jenkins --version
ubuntu@ip-172-31-95-197:~$ jenkins --version
2.462.2
```

#### Verify the Trivy version

```bash
# trivy --version
ubuntu@ip-172-31-95-197:~$ trivy --version
Version: 0.55.0
```

#### Verify the Docker & Docker Scout version

```bash
# docker --version
# docker-scout version

ubuntu@ip-172-31-95-197:~$ docker --version
Docker version 24.0.7, build 24.0.7-0ubuntu4.1


docker ps -a
CONTAINER ID   IMAGE                     COMMAND                  CREATED          STATUS          PORTS                                       NAMES
0b976c169a37   sonarqube:lts-community   "/opt/sonarqube/dock…"   12 minutes ago   Up 12 minutes   0.0.0.0:9000->9000/tcp, :::9000->9000/tcp   sonar


ubuntu@ip-172-31-95-197:~$ docker-scout version

      ⢀⢀⢀             ⣀⣀⡤⣔⢖⣖⢽⢝
   ⡠⡢⡣⡣⡣⡣⡣⡣⡢⡀    ⢀⣠⢴⡲⣫⡺⣜⢞⢮⡳⡵⡹⡅
  ⡜⡜⡜⡜⡜⡜⠜⠈⠈        ⠁⠙⠮⣺⡪⡯⣺⡪⡯⣺
 ⢘⢜⢜⢜⢜⠜               ⠈⠪⡳⡵⣹⡪⠇
 ⠨⡪⡪⡪⠂    ⢀⡤⣖⢽⡹⣝⡝⣖⢤⡀    ⠘⢝⢮⡚       _____                 _
  ⠱⡱⠁    ⡴⡫⣞⢮⡳⣝⢮⡺⣪⡳⣝⢦    ⠘⡵⠁      / ____| Docker        | |
   ⠁    ⣸⢝⣕⢗⡵⣝⢮⡳⣝⢮⡺⣪⡳⣣    ⠁      | (___   ___ ___  _   _| |_
        ⣗⣝⢮⡳⣝⢮⡳⣝⢮⡳⣝⢮⢮⡳            \___ \ / __/ _ \| | | | __|
   ⢀    ⢱⡳⡵⣹⡪⡳⣝⢮⡳⣝⢮⡳⡣⡏    ⡀       ____) | (_| (_) | |_| | |_
  ⢀⢾⠄    ⠫⣞⢮⡺⣝⢮⡳⣝⢮⡳⣝⠝    ⢠⢣⢂     |_____/ \___\___/ \__,_|\__|
  ⡼⣕⢗⡄    ⠈⠓⠝⢮⡳⣝⠮⠳⠙     ⢠⢢⢣⢣
 ⢰⡫⡮⡳⣝⢦⡀              ⢀⢔⢕⢕⢕⢕⠅
 ⡯⣎⢯⡺⣪⡳⣝⢖⣄⣀        ⡀⡠⡢⡣⡣⡣⡣⡣⡃
⢸⢝⢮⡳⣝⢮⡺⣪⡳⠕⠗⠉⠁    ⠘⠜⡜⡜⡜⡜⡜⡜⠜⠈
⡯⡳⠳⠝⠊⠓⠉             ⠈⠈⠈⠈

version: v1.13.0 (go1.22.5 - linux/amd64)
git commit: 7a85bab58d5c36a7ab08cd11ff574717f5de3ec2
ubuntu@ip-172-31-95-197:~$
```

#### Verify the Cloud Watch alert

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116101100/6afd81c8-d6bc-4349-92a8-61c151f4fdd2.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116105473/ab2c96b0-f5e3-4d16-b1d3-1f8d57e7bd8e.png align="center")

#### Verify the SNS & Topics

Once you open your Gmail account, find the AWS notification subscription confirmation email, and click on `confirm subscription` for the topic subscription.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116117248/422cf9f3-245d-4391-bfbe-246af7918965.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116123172/0c0c47a7-452b-447a-87d9-ab3d2e62b4ea.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116126172/7478913c-1791-4d58-86b7-7aae04e599e0.png align="center")

## Setup the Jenkins

Note down the public address of the VM and access it in the browser

```bash
<publicIP of VM :8080>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116144353/cbe8d964-b357-4ae9-915a-c7e2e594be72.png align="center")

will run this command on the VM `sudo cat /var/lib/jenkins/secrets/initialAdminPassword` to get the first-time login password.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116164333/c982e86a-3377-4a88-95a1-e5a5e589fd29.png align="center")

#### Install `Plug-in` in Jenkins.

We will install the following `plug-in` in Jenkins.

Dashboard⇒ Manage Jenkins⇒ plug-in

```bash
Eclipse Temurin installer
NodeJS
SonarQube Scanner
Docker
Docker Common
Docker Pipeline
Docker API
OWASP Dependency-Check
Email Extension Template
Pipeline: Stage View
Blue Ocean (Optional)
```

#### Configure the tools in Jenkins.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116181853/047bc75d-5cac-41f3-a6bf-1b4a8facbce7.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116185797/8d7d305a-9f75-4d54-8a4b-efe26264ba6f.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116191287/80663132-c026-4e35-90f9-2ac60a830b67.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116196769/913196c6-c196-4bea-bfe8-0122d813df55.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116202356/c4ce90d2-c902-49ec-87c5-0cf05c7b0543.png align="center")

## Setup the SonarQube

As we installed SonarQube as a container on the same Jenkins VM. So, we will select the public IP address of Jenkins.

```bash
<publicIP of VM :9000>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116214621/48150c27-e034-4f03-a326-eb0da53abb5c.png align="center")

* The default username & password is `admin/admin` and you need to change it.
    

#### Intigrate SonarQube to Jenkins and vise-versa

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116227946/2089b3d0-26b6-4c48-9695-f52998159882.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116234145/d4120644-aba7-40a3-b166-6b97684f6af0.png align="center")

#### On Jenkins UI Console-

```bash
Dashboard
Manage Jenkins
Credentials
System
Global credentials (unrestricted)
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116245643/a33d5bff-20ed-493f-9bfb-f1cdad7ac4b3.png align="center")

#### Configure QualityGateway: (SonarQube)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116263648/9bcd3e1c-a73f-4000-9b8f-1f3b11ca8e23.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116268638/a8fe7ce0-2fba-4b10-9dcb-2abc9e43b9c5.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116272849/9c694f96-10da-4b22-a5b0-26499297156b.png align="center")

#### on Jenkins UI-

```bash
Dashboard
Manage Jenkins
System
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116282850/1474634e-7676-434c-bc59-423a0d56d712.png align="center")

#### Configure the Docker credential

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116293222/e1605008-3de2-421d-9cc7-dedbf3162339.png align="center")

#### Extended E-mail Notification

* configure the password for email notification apps
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116309212/cb2043ab-b3d5-4be0-bf49-ff548269fe29.png align="center")
    
    #### e-mail notification setup
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116319083/eb97e4ed-5983-4763-bdfc-49eedb6e6d9d.png align="center")
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116323240/56dbbdac-0ae0-4d03-96d5-7d65b86d6bb1.png align="center")
    
* got email in Gmail inbox-
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116339060/14e16a8b-99bc-44c5-99fe-8642ac526775.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116343163/10e05bd7-3ecf-434d-a4a5-eddf3293fea3.png align="center")
    
    ## Create a Pipeline for CI/CD
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116361544/1cd1aa03-502a-4065-9a4b-dc62aed80e0c.png align="center")
    
    ```sh
    pipeline {
        agent any
        tools {
            jdk 'jdk17'
            nodejs 'node16'
        }
        environment {
            SCANNER_HOME=tool 'sonar-scanner'
        }
        stages {
            stage ("clean workspace") {
                steps {
                    cleanWs()
                }
            }
            stage ("Git checkout") {
                steps {
                    git branch: 'main', url: 'https://github.com/mrbalraj007/starbucks.git'
                }
            }
            stage("Sonarqube Analysis "){
                steps{
                    withSonarQubeEnv('sonar-server') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=starbucks \
                        -Dsonar.projectKey=starbucks '''
                    }
                }
            }
            stage("quality gate"){
               steps {
                    script {
                        waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token' 
                    }
                } 
            }
            stage("Install NPM Dependencies") {
                steps {
                    sh "npm install"
                }
            }
            stage('OWASP FS SCAN') {
                steps {
                    dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
            stage ("Trivy File Scan") {
                steps {
                    sh "trivy fs . > trivy.txt"
                }
            }
            stage ("Build Docker Image") {
                steps {
                    sh "docker build -t starbucks ."
                }
            }
            stage ("Tag & Push to DockerHub") {
                steps {
                    script {
                        withDockerRegistry(credentialsId: 'docker') {
                            sh "docker tag starbucks balrajsi/starbucks:latest "
                            sh "docker push balrajsi/starbucks:latest "
                        }
                    }
                }
            }
            stage('Docker Scout Image') {
                steps {
                    script{
                       withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                           sh 'docker-scout quickview balrajsi/starbucks:latest'
                           sh 'docker-scout cves balrajsi/starbucks:latest'
                           sh 'docker-scout recommendations balrajsi/starbucks:latest'
                       }
                    }
                }
            }
            stage ("Deploy to Conatiner") {
                steps {
                    sh 'docker run -d --name starbucks -p 3000:3000 balrajsi/starbucks:latest'
                }
            }
        }
        post {
        always {
            emailext attachLog: true,
                subject: "'${currentBuild.result}'",
                body: """
                    <html>
                    <body>
                        <div style="background-color: #FFA07A; padding: 10px; margin-bottom: 10px;">
                            <p style="color: white; font-weight: bold;">Project: ${env.JOB_NAME}</p>
                        </div>
                        <div style="background-color: #90EE90; padding: 10px; margin-bottom: 10px;">
                            <p style="color: white; font-weight: bold;">Build Number: ${env.BUILD_NUMBER}</p>
                        </div>
                        <div style="background-color: #87CEEB; padding: 10px; margin-bottom: 10px;">
                            <p style="color: white; font-weight: bold;">URL: ${env.BUILD_URL}</p>
                        </div>
                    </body>
                    </html>
                """,
                to: 'provide_your_Email_id_here',
                mimeType: 'text/html',
                attachmentsPattern: 'trivy.txt'
            }
        }
    }
    ```
    
    #### Pipeline Status
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116385963/ae8e7f2b-c6b3-46a7-a416-baa14f93b462.png align="center")
    
    #### Email Notification for a successful build.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116396174/59960f05-3dc2-43c3-b0f9-a6a5f2d1ec59.png align="center")
    
    #### To verify the container image in `Docker hub`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116436076/003bf9db-4e38-4a05-ad1f-8f83427c6cd8.png align="center")
    
    #### To verify the container image in `Jinkins Server`
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116447307/be574b6a-b8b0-4eef-873a-43143e57e05c.png align="center")
    
    #### To verify the application
    
    As we build the container on the same Jenkins VM. So, we will select the public IP address of Jenkins.
    
    ```bash
    <publicIP of VM :3000>
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116463093/8cf9e787-cf49-4460-b461-91b30eefd099.png align="center")
    
    #### EC2 resource details
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116472146/cde614e8-2b71-47b7-b7be-d507bdcf24c3.png align="center")
    
    #### CloudWatch Status.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116482195/5230a6b1-89d1-4bae-8b8c-65e15de9529f.png align="center")
    
    #### Environment Cleanup:
    
    * As we are using Terraform, we will use the following command to delete the environment
        
    
    ```bash
    terraform destroy --auto-approve
    ```
    
    #### Time to delete the Virtual machine.
    
    Go to folder *"11.Real-Time-DevOps-Project/Terraform\_Code"* and run the terraform command.
    
    ```bash
    cd Terraform_Code/
    
    $ ls -l
    Mode                 LastWriteTime         Length Name
    ----                 -------------         ------ ----
    da---l          10/09/24   9:48 PM                Terraform_Code
    
    Terraform destroy --auto-approve
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1726116501520/583671fc-3447-4b76-8870-ac129b312602.png align="center")
    
    #### Key Takeaways:
    
    * Automated CI/CD Pipeline: Automating the build, test, and deployment process improves efficiency and reduces manual intervention.
        
    * Error Handling: Ensure all required tools and configurations are in place to avoid build failures.
        
    * Monitoring and Alerts: Utilize CloudWatch and SNS to monitor system performance and receive timely notifications.
        
    * Enhanced Security: Integration of OWASP, Docker Scout, and Dependency-Check ensures a secure deployment pipeline.
        
    * Code Quality: SonarQube helps maintain high code quality standards.
        
    
    ## What to Avoid:
    
    * Skipping Security Scans: Always include security scans to identify and mitigate vulnerabilities.
        
    * Ignoring Resource Monitoring: Regularly check resource utilization to prevent potential issues. Overlooking Dependency Management: Regularly update and check dependencies for vulnerabilities.
        
    
    ## Key Benefits:
    
    * *Efficiency: Automating pipeline stages saves time and reduces manual errors*.
        
    * *Security: Regular scans with Docker Scout and OWASP Dependency-Check help identify and fix vulnerabilities*.
        
    * *Reliability: Continuous integration and deployment ensure consistent and reliable application updates.*
        
    
    ## Why Use This Project:
    
    * *Deploying a Starbucks clone using this approach not only demonstrates practical skills in integrating various DevSecOps tools but also highlights the importance of security and efficiency in modern software deployment. It showcases how to build, test, and deploy applications securely and efficiently, leveraging cloud services and CI/CD practices.*
        
    
    ## Use Case:
    
    * *This setup is ideal for development teams looking to implement a robust CI/CD pipeline to automate their build, test, and deployment processes. It ensures high-quality code delivery with continuous monitoring and security checks.*
        
    
    **Ref Link**
    
    * [YouTube Link](https://www.youtube.com/watch?v=N_AEbtTLcgY&list=PLJcpyd04zn7rZtWrpoLrnzuDZ2zjmsMjz&index=77)
        
    * [Docker Scout](https://docs.docker.com/scout/)
        
    * [What Is Docker Scout and How to Use It](https://earthly.dev/blog/docker-scout/)