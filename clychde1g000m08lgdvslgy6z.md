---
title: "What is Trivy and how do you use it?"
datePublished: Mon Jul 08 2024 04:26:57 GMT+0000 (Coordinated Universal Time)
cuid: clychde1g000m08lgdvslgy6z
slug: what-is-trivy-and-how-do-you-use-it
tags: trivy

---

---

## About Trivy:

**Trivy**, developed by Aqua Security, is an open-source vulnerability scanner specifically designed for container images. It stands out for its ease of use, efficiency, and comprehensive scanning capabilities. Trivy is a valuable tool for developers and operations teams who aim to maintain secure containerized environments by identifying vulnerabilities and misconfigurations.

### Use Cases for Trivy:

* Early Detection of Vulnerabilities: By integrating Trivy into CI/CD pipelines, organizations can detect vulnerabilities in container images at the earliest possible stage. This helps in maintaining a robust security posture and reduces the cost and effort required to address vulnerabilities later in the lifecycle.
    
* Compliance and Auditing: Trivy’s comprehensive scanning capabilities help organizations adhere to security policies and regulatory requirements. It provides detailed reports on vulnerabilities, which are essential for auditing and compliance purposes.
    
* Securing Container Registries: Scanning images in container registries ensures that only secure and compliant images are available for deployment. This minimizes the risk of deploying vulnerable images to production environments.
    
* Runtime Security: In Kubernetes environments, Trivy can continuously monitor running containers for vulnerabilities, ensuring that any newly discovered vulnerabilities are promptly identified and addressed.
    

### Benefits of Using Trivy:

* Ease of Use: Trivy is designed with simplicity, making integrating into various workflows easy. It requires minimal configuration and can be used with a single command, making it accessible to developers and security teams alike.
    
* Comprehensive Scanning: Trivy performs thorough scans of container images, including operating system packages and application dependencies. It covers a wide range of vulnerabilities, providing a comprehensive security assessment.
    
* Speed and Efficiency: Trivy is optimized for speed, allowing for quick scans without compromising accuracy. This is crucial in CI/CD environments where speed and efficiency are paramount.
    
* Open Source and Community Support: As an open-source tool, Trivy benefits from a vibrant community that contributes to its development and maintenance. This ensures continuous improvement and up-to-date vulnerability databases.
    
* Integration Capabilities: Trivy integrates seamlessly with popular CI/CD tools, container registries, and Kubernetes, providing flexibility and ease of adoption in various environments.
    

## **How I set in my Project**:

***Environment Setup:***

| Host Name | IP Address | OS |
| --- | --- | --- |
| trivy | 192.168.1.60 | Ubuntu 24.04 LTS |

***Note***\--&gt; Will install `jenkins` on the same machine as the plug-in is not available for `trivy`

### How to install it:

Ref Link

```powershell
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update -y
sudo apt-get install trivy -y
```

* will use the repo and will clone it on trivy machine: `git clone https://github.com/mrbalraj007/Boardgame.git`
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412309934/16fcd104-1910-4ee4-a9c5-3764ef9a9d09.png align="left")

`Boardgame` folder is created and will scan it.

will run the following command:

```sh
trivy fs .
```

*To properly set the outcome in the report*

```sh
trivy fs --format table -o trivy-fsreport.html .
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412356544/a8c857d1-99eb-473c-b712-ad6a8cf95be5.png align="center")

#### Install docker

* Will install the docker on the same `trivy` machine and will the permission as below
    

```sh
sudo apt  install docker.io -y

# To change the ownership to current user
sudo chown $USER /var/run/docker.sock

# Add your user to the Docker group:
sudo usermod -aG docker $USER
```

#### Verify Docker version

```powershell
docker --version
Docker version 24.0.7, build 24.0.7-0ubuntu4
```

* Will pull the image and will scan it: `image=sonarqube:lts-community`
    

```sh
docker pull sonarqube:lts-community
```

#### Now, We will scan the docker image.

* Will scan the docker image:
    

```powershell
trivy image --format table -o trivy-image-report.html sonarqube:lts-community
```

### Using Jenkins:

* configure the `maven` first.
    

Dashboard &gt; Manage Jenkins&gt; Tools:

click on Add under *Maven installations*.

```bash
name: maven3
Version: 3.9.8
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412397301/d4bc350d-554b-4cb5-85b3-ef6e5c6dc027.png align="center")

will use Jenkins on the same server and create a declarative pipeline.

* Here we have scanned the FS only
    

```bash
pipeline {
    agent any
    tools {
        maven 'maven3'
          } 
    stages {
        stage('Git CheckOut') {
            steps {
                git branch: 'main', url: 'https://github.com/mrbalraj007/Boardgame.git'
            }
        }
    stage('Compile and Test') {
            steps {
                sh 'mvn test'
            }
        } 
    stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-fsreport.html .'
            }
        }
    }
}
```

* \`\`\`bash pipeline { agent any tools { maven 'maven3' } stages { stage('Git CheckOut') { steps { git branch: 'main', url: 'https://github.com/mrbalraj007/Boardgame.git' } } stage('Compile and Test') { steps { sh 'mvn test' } } stage('Trivy FS Scan') { steps { sh 'trivy fs --format table -o trivy-fsreport.html .' } } } } \`\`\`
    
* Pipeline has been executed successfully.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412472616/3eb655f0-d078-4bc5-bda8-16f9e65e2b95.png align="center")
    
* output generated in the workspace.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412484040/bdba5388-63d9-4dc0-b412-5f9734baf5bf.png align="center")
    
* Here we have scanned the FS + Docker image
    

```sh
pipeline {
    agent any
    tools {
        maven 'maven3'
          } 
    stages {
        stage('Git CheckOut') {
            steps {
                git branch: 'main', url: 'https://github.com/mrbalraj007/Boardgame.git'
            }
        }
    stage('Compile and Test') {
            steps {
                sh 'mvn test'
            }
        } 
    stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-fsreport.html .'
            }
        }
    stage('Trivy image Scan') {
            steps {
                sh 'trivy image --format table -o trivy-image-report.html sonarqube:lts-community'
            }
        }
    }
}
```

Pipeline Status:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412517453/9ee880f0-6e7e-40b1-9338-fab492eda315.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412523337/21033c64-1e8f-4c38-9978-52cf36f97250.png align="center")

Workspace:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720412530153/8237d51c-8f17-4a70-b1bf-f19dad06c261.png align="center")