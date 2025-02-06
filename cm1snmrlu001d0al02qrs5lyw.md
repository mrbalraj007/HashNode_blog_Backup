---
title: "Simple Notes App for Community: End-to-End Implementation using CI/CD""
datePublished: Thu Oct 03 2024 02:05:38 GMT+0000 (Coordinated Universal Time)
cuid: cm1snmrlu001d0al02qrs5lyw
slug: simple-notes-app-for-community-end-to-end-implementation-using-cicd
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1727921040429/c9a36472-eec9-430a-a008-e0213c5a4b46.gif
tags: website, aws, github, terraform, jenkins, docker-compose, codenewbies, docker-images, dockerhub

---

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

* \[x\] [Clone repository for terraform code](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/14.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * from the Virtual machine [main.tf](http://main.tf) (i.e key name- `MYLABKEY`\*)
        
* \[x\] [App Repo](https://github.com/mrbalraj007/django-notes-app.git)
    
* \[x\] **Basic Knowledge of GitHub**: You need to be familiar with version control, pull requests, and branching.
    
* \[x\] **Understanding of Docker**: Docker is used for containerizing the application. Make sure you know how to create, manage, and run Docker containers.
    
* \[x\] **Familiarity with Jenkins**: Jenkins is the CI tool that automates the testing and deployment process. A basic understanding of setting up Jenkins pipelines is essential.
    
* \[x\] **Experience with Python and Node.js**: Since the backend of the app is likely in Python and the frontend in React (which relies on Node.js), make sure you're comfortable with both technologies.
    
* \[x\] **React Framework**: This is used to build the user interface. Familiarity with React components, state management, and hooks is important.
    

## Setting Up the Environment

I have created a Terraform code to set up the entire environment, including the installation of required applications, and tools.

* ⇒ Two EC2 machines will be created named "Jenkins Server & Agent"
    
* ⇒ Docker Install
    

### Setting Up the Virtual Machines (EC2)

First, we'll create the necessary virtual machines using `terraform`.

Below is a terraform configuration:

Once you [clone the repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git) then go to folder *"14.Real-Time-DevOps-Project/Terraform\_Code/Code\_IAC\_Terraform\_box"* and run the terraform command.

```bash
cd Terraform_Code/Code_IAC_Terraform_box

$ ls -l
da---l          29/09/24  12:02 PM                k8s_setup_file
-a---l          29/09/24  10:44 AM            507 .gitignore
-a---l          01/10/24  10:50 AM           3771 agent_install.sh
-a---l          01/10/24  10:59 AM           8149 main.tf
-a---l          16/07/21   4:53 PM           1696 MYLABKEY.pem
-a---l          25/07/24   9:16 PM            239 provider.tf
-a---l          01/10/24  11:26 AM          10257 terrabox_install.sh
```

**Note** ⇒ Make sure to run [`main.tf`](http://main.tf) from inside the folders.

```bash
13.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box/

da---l          29/09/24  12:02 PM                k8s_setup_file
-a---l          29/09/24  10:44 AM            507 .gitignore
-a---l          01/10/24  10:50 AM           3771 agent_install.sh
-a---l          01/10/24  10:59 AM           8149 main.tf
-a---l          16/07/21   4:53 PM           1696 MYLABKEY.pem
-a---l          25/07/24   9:16 PM            239 provider.tf
-a---l          01/10/24  11:26 AM          10257 terrabox_install.sh
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

Once you run the terraform command, then we will verify the following things to make sure everything is set up via a terraform.

### Inspect the `Cloud-Init` logs:

Once connected to the EC2 instance then you can check the status of the `user_data` script by inspecting the [log files](13.Real-Time-DevOps-Project%5Ccloud-init-output.log).

```bash
# Primary log file for cloud-init
sudo tail -f /var/log/cloud-init-output.log
```

* If the user\_data script runs successfully, you will see output logs and any errors encountered during execution.
    
* If there’s an error, this log will provide clues about what failed.
    

Outcome of "`cloud-init-output.log`"

![image](https://github.com/user-attachments/assets/bb628a4f-36be-4af9-998f-2165fb837046 align="left")

![image-1](https://github.com/user-attachments/assets/cd0b6335-681c-4773-b233-325efbaa7ac1 align="left")

### Verify the Installation

* \[x\] Docker version
    

```bash
ubuntu@ip-172-31-95-197:~$ docker --version
Docker version 24.0.7, build 24.0.7-0ubuntu4.1


docker ps -a
ubuntu@ip-172-31-94-25:~$ docker ps
```

* \[x\] Terraform version
    

```bash
ubuntu@ip-172-31-89-97:~$ terraform version
Terraform v1.9.6
on linux_amd64
```

* \[x\] AWS Cli version
    

```bash
ubuntu@ip-172-31-89-97:~$ aws version
usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run:
  aws help
  aws <command> help
  aws <command> <subcommand> help
```

<details><summary><b><span>Change the hostname: (optional)</span></b></summary><br />
<p>sudo terraform show</p>
<pre><code class="language-bash">sudo hostnamectl set-hostname jenkins-svr
sudo hostnamectl set-hostname jenkins-agent</code></pre>
<ul>
<li>Update the /etc/hosts file:
<ul>
<li>Open the file with a text editor, for example:</li>
</ul>
</li>
</ul>
<pre><code class="language-bash">sudo vi /etc/hosts</code></pre>
<p>Replace the old hostname with the new one where it appears in the file.</p>
<p>Apply the new hostname without rebooting:</p>
<pre><code class="language-bash">sudo systemctl restart systemd-logind.service</code></pre>
<p>Verify the change:</p>
<pre><code class="language-bash">hostnamectl</code></pre>
<p>Update the package</p>
<pre><code class="language-bash">sudo -i
apt update</code></pre>
</details>

## Setup the Jenkins

Access Jenkins via http://:8080. Retrieve the initial admin password using:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![image-5](https://github.com/user-attachments/assets/4f51816f-190a-4a43-a7a1-e6a93a1bedb0 align="left")

![image-6](https://github.com/user-attachments/assets/d2c70de6-debc-4bec-9208-b36f0b308a32 align="left")

### Setup the Jenkins agent

* [Set the password](https://www.cyberciti.biz/faq/change-root-password-ubuntu-linux/) for user "ubuntu" on both Jenkins Master and Agent machines.
    

```sh
sudo passwd ubuntu
```

![image-3](https://github.com/user-attachments/assets/57fae1e1-7dbc-4b0f-877e-0266c83af6bc align="left")

* Need to do the password-less authentication between both servers.
    

```bash
sudo su
cat /etc/ssh/sshd_config | grep "PasswordAuthentication"
echo "PasswordAuthentication yes" >> /etc/ssh/sshd_config
cat /etc/ssh/sshd_config | grep "PasswordAuthentication"

cat /etc/ssh/sshd_config | grep "PermitRootLogin"
echo "PermitRootLogin yes"  >> /etc/ssh/sshd_config
cat /etc/ssh/sshd_config | grep "PermitRootLogin"
```

* Restart the sshd reservices.  
    

```bash
systemctl daemon-reload
      or 
sudo service ssh restart
```

* Generate the SSH key and share it with the agent.
    

```bash
ssh-keygen
```

* Copy the public SSH key from Jenkins to Agent.
    
    * The public key from Jenkins master.
        

```bash
ubuntu@ip-172-31-89-97:~$ cat ~/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIG4BFDIh47LkE6huSzi6ryMKcw+Rj1+6ErnplFbOK5Nz ubuntu@ip-172-31-89-97
```

From Agent.

![image-4](https://github.com/user-attachments/assets/5ac10b8d-2ef1-426e-9e0c-6971cfb4f03a align="left")

Now, try to do the SSH to agent, and it should be connected without any credentials.

```bash
ssh ubuntu@<private IP address of agent VM>
```

![image-7](https://github.com/user-attachments/assets/475ddd3a-fb0c-40ce-857d-af6cfbef0a08 align="left")

Open Jenkins UI and configure the agent. Dashboard&gt; Manage Jenkins&gt; Nodes

Remote root directory: define the path. Launch method: Launch agents via ssh

* Host: public IP address of agent VM
    
* Credential of the agent. (will create the credential)
    
    * Kind: SSH Username with private key
        
    * private key from Jenkins Master server.
        
        ![image-10](https://github.com/user-attachments/assets/35a4974d-9447-4b7a-b074-4151e4691708 align="left")
        
* Host Key Verification Strategy: Non-Verifying Verification Strategy
    

![image-8](https://github.com/user-attachments/assets/ece7e0e6-d398-42ae-8d90-45d8bd9445e9 align="left")

![image-9](https://github.com/user-attachments/assets/6b865751-770d-45e4-a991-9d82feb0a44d align="left")

![image-11](https://github.com/user-attachments/assets/b276a604-bc28-432b-a8cd-51ce6062e024 align="left")

Congratulations: the Agent is successfully configured and alive.

![image-12](https://github.com/user-attachments/assets/4bf6a9fc-5579-4ce5-bfe4-2eedef303579 align="left")

### Install the plugin in Jenkins

```sh
Blue Ocean
Pipeline: Stage View
```

* Run any job and verify that the job is executing on the agent node.
    
    * create a below pipeline build it and verify the outcomes in the agent machine.
        

```bash
pipeline {
    agent { label "balraj"}

    stages {
        stage('code') {
            steps {
                echo 'This is cloning the code'
                git branch: 'main', url: 'https://github.com/mrbalraj007/django-notes-app.git'
                echo "This is cloning the code"
            }
        }
    }
}
```

![image-13](https://github.com/user-attachments/assets/6671ba4f-690e-47f6-be6a-217a7fe2ede8 align="left")

* Build a Docker image. add the following state to main pipeline
    

```sh
         stage('build') {
           steps {
               echo 'This is building the docker image'
               sh "docker build -t notes-app:latest ."
               echo "Image has been created successfully"
           }
       }
```

![image-14](https://github.com/user-attachments/assets/64d2b951-5a58-4ab0-b471-e6cd6b5559fd align="left")

* Add the below the deploy the image.
    

```sh
 stage('test') {
            steps {
                echo 'This is testing the code'
            }
        }
        stage('Deploy') {
            steps {
                echo 'This is deploying the code'
                sh 'docker run -d -p 8000:8000 notes-app:latest '
            }
        }
```

![image-15](https://github.com/user-attachments/assets/f7f25d1c-187c-4271-a37a-211c9a95c895 align="left")

```bash
pipeline {
    agent { label "balraj"}

    stages {
        stage('code') {
            steps {
                echo 'This is cloning the code'
                git branch: 'main', url: 'https://github.com/mrbalraj007/django-notes-app.git'
                echo "This is cloning the code"
            }
        }
        stage('build') {
            steps {
                echo 'This is building the docker image'
                sh "docker build -t notes-app:latest ."
                echo "Image has been created successfully"
            }
        }
        stage('test') {
            steps {
                echo 'This is testing the code'
            }
        }
        stage('Deploy') {
            steps {
                echo 'This is deploying the code'
                sh 'docker run -d -p 8000:8000 notes-app:latest '
            }
        }
    }
}
```

Now, try to access it via the 8000 port

If you rerun the build, then you will get an error because port 8000 has already been used. So we will use here Docker Compose.

![image-17](https://github.com/user-attachments/assets/b76a7f1e-ec81-44ad-8c7a-6dd8f869969f align="left")

here is the updated pipeline

```bash
pipeline {
    agent { label "balraj"}

    stages {
        stage('code') {
            steps {
                echo 'This is cloning the code'
                git branch: 'main', url: 'https://github.com/mrbalraj007/django-notes-app.git'
                echo "This is cloning the code"
            }
        }
        stage('build') {
            steps {
                echo 'This is building the docker image'
                sh "docker build -t notes-app:latest ."
                echo "Image has been created successfully"
            }
        }
        stage('test') {
            steps {
                echo 'This is testing the code'
            }
        }
        stage('Deploy') {
            steps {
                echo 'This is deploying the code'
                sh 'docker compose up -d'
            }
        }
    }
}
```

Kill the existing image/deployment before build/executing it.

```bash
docker container ls
docker stop aa48f961a5de && docker rm aa48f961a5de
```

![image-18](https://github.com/user-attachments/assets/2f44ca33-9edc-4c2c-8ae8-456b2c9b725e align="left")

![image-19](https://github.com/user-attachments/assets/cb3c2969-9844-45ca-9cd7-2d121e614b49 align="left")

* Push image to Docker hub.
    
    * create credential in Jenkins for Dockerhub
        
        ![image-20](https://github.com/user-attachments/assets/e7ec7dd7-45d8-45fc-beef-7cabef19a6e5 align="left")
        
* Bind credential in the pipeline
    

```bash
pipeline {
    agent { label "balraj"}

    stages {
        stage('code') {
            steps {
                echo 'This is cloning the code'
                git branch: 'main', url: 'https://github.com/mrbalraj007/django-notes-app.git'
                echo "This is cloning the code"
            }
        }
        stage('build') {
            steps {
                echo 'This is building the docker image'
                sh "docker build -t notes-app:latest ."
                echo "Image has been created successfully"
            }
        }
        stage('test') {
            steps {
                echo 'This is testing the code'
            }
        }
        stage('Push to DockerHub') {
            steps {
                echo "This is pushing image to Docker Hub"
                withCredentials([usernamePassword(credentialsId:"dockerHubCred",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker image tag notes-app:latest ${env.dockerHubUser}/notes-app:latest"
                sh "docker push ${env.dockerHubUser}/notes-app:latest"
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'This is deploying the code'
                sh 'docker compose up -d'
            }
        }
    }
}
```

![image-21](https://github.com/user-attachments/assets/8e5ecc07-77b3-410d-b3c2-e10f2686d901 align="left")

* Creating a [webhook](https://docs.github.com/en/webhooks/using-webhooks/creating-webhooks) for Jenkins in Github
    
* Go to repo setting and click on webhooks PayloadURL: [http://54.144.163.163:8080/github-webhook/](http://54.144.163.163:8080/github-webhook/) (Jenkins URL with port)
    
* Content type: Application/JSON
    

![image-22](https://github.com/user-attachments/assets/688e5d89-da8a-4a6d-8877-6e3f675625aa align="left")

> \[!Note\] I was getting a 302 error message, and when I followed the below procedure, it fixed itself.

* I clicked on webhooks clicked on the recent deliveries and clicked on redeliver and the issue was fixed.
    
    ![image-23](https://github.com/user-attachments/assets/2191c55c-6693-4663-88d4-ce5110329c22 align="left")
    
    ![image-24](https://github.com/user-attachments/assets/a12e92f4-9650-4d62-8a3e-6c9f525899b7 align="left")
    

Now, we have to tick this option in Jenkins: "GitHub hook trigger for GITScm polling."

![image-25](https://github.com/user-attachments/assets/ca734da6-ef5a-454b-a516-9b0c9dcff4d2 align="left")

Try to modify anything in the Github repo and the build should be auto trigger.

![image-26](https://github.com/user-attachments/assets/514af65a-448f-462b-8a57-b9afd0a875de align="left")

it works :-)

**Ref Link**

* [YouTube Link](https://www.youtube.com/watch?v=XaSdKR2fOU4&t=21621s)