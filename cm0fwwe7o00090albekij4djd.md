---
title: "Building a Robust CICD Pipeline: Deploying Kubernetes Apps with Jenkins"
datePublished: Thu Aug 29 2024 23:24:21 GMT+0000 (Coordinated Universal Time)
cuid: cm0fwwe7o00090albekij4djd
slug: building-a-robust-cicd-pipeline-deploying-kubernetes-apps-with-jenkins
tags: docker, sonarqube, kubernetes, terraform, pipeline, nexus, grafana, kubernetes-container, trivy, kubernetes-architecture, grafana-monitoring, terraform-aws-infrastructureascode-provisioning-automation-cloudcomputing, grafana-dashboard, maven-build-tool, devsecops-cicd-pipeline-sonarqube-trivy-owasp-jenkins-security-continuousintegration-continuousdeployment-automation-codequality-containersecurity-vulnerabilityscanning-securecoding-softwaredevelopment-devops-cybersecurity-applicationsecurity-softwaresecurity-techblog-itsecurity-codeanalysis-securitytools

---

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973586534/f970ebc5-cda9-4992-8e3b-63d50d97de80.png align="center")

In this blog, we’ll guide you through deploying a Kubernetes application using Jenkins and integrating tools like `GitHub, Trivy, SonarQube, Nexus, Grafana, Docker, and Prometheus`. We will cover the setup, deployment, and monitoring stages to ensure a smooth and efficient pipeline.

Creating a complete CI/CD pipeline involves using different tools and technologies to automate the software development process from code integration to deployment. Here's a high-level overview of how each tool fits into the pipeline:

1. **Jenkins**:: An open-source automation server for building, deploying, and automating tasks.
    
2. **GitHub**: Serves as the version control system to manage code changes and history.
    
3. **Maven**: A build automation tool used for Java projects, Maven compiles the source code and packages it into a deployable format.
    
4. **Trivy**: An open-source vulnerability scanner for container images, ensuring the security of the application.
    
5. **SonarQube**: Analyzes source code quality and provides reports on code smells, bugs, and vulnerabilities.
    
6. **Docker**: Packages the application and its dependencies into a container, making it easy to deploy anywhere.
    
7. **Terraform**: Infrastructure as Code (IaC) tool that allows you to define and provision infrastructure using a high-level configuration language.
    
8. **Kubernetes**: An orchestration platform for managing containerized applications, handling scaling and failover.
    
9. **Prometheus**: A monitoring system that collects metrics from configured targets at given intervals, evaluates rule expressions, and displays results.
    
10. **Grafana**: A visualization tool that allows you to create dashboards for your metrics, giving insights into the application's performance and health.
    

*By linking these tools together, developers can automate the testing, building, scanning, and deployment of applications, resulting in more efficient and reliable software delivery. The pipeline starts with a developer pushing code to GitHub, which triggers Maven to build the application. After the build, Trivy scans the Docker container for vulnerabilities and SonarQube checks for code quality issues. If all checks pass, Terraform provisions the necessary infrastructure, and Kubernetes deploys the application. Prometheus monitors the system, and Grafana provides a dashboard for real-time analytics. This pipeline demonstrates a strong DevOps practice, ensuring continuous integration and delivery with a focus on code quality and security.*

### Prerequisites

1. [Clone repository for terraform code](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git)
    
2. [App Repo](https://github.com/mrbalraj007/FullStack-Blogging-App)
    
3. Domain name ( optional )
    

### Setting Up the Environment

I have created a Terraform file to set up the entire environment, including the installation of required applications and tools.

* Setting Up the Virtual Machines (EC2)
    

First, we'll create the necessary virtual machines using `terraform`.

Below is a terraform configuration:

### Once you [clone repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git) then go to folder *"****08.Real-Time-DevOps-Project/Terraform\_Code****"* and run the terraform command.

```bash
cd Terraform_Code/

$ ls -l
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
da---l          25/08/24   8:43 PM                01.Code_IAC_Jenkins_Trivy
da---l          25/08/24   8:41 PM                02.Code_IAC_Nexus
da---l          25/08/24   8:39 PM                03.Code_IAC_SonarQube
da---l          26/08/24   9:48 AM                04.Code_IAC_Terraform_box
da---l          25/08/24   8:38 PM                05.Code_IAC_Grafana
-a---l          20/08/24   1:45 PM            493 .gitignore
-a---l          20/08/24   4:54 PM           1589 main.tf
```

You need to run `main.tf` file using the following terraform command.

**Note** ⇒ Make sure to run `main.tf` from outside the folders; do not go inside the folders.

```bash
cd 08.Real-Time-DevOps-Project/Terraform_Code

da---l          25/08/24   8:43 PM                01.Code_IAC_Jenkins_Trivy
da---l          25/08/24   8:41 PM                02.Code_IAC_Nexus
da---l          25/08/24   8:39 PM                03.Code_IAC_SonarQube
da---l          26/08/24   9:48 AM                04.Code_IAC_Terraform_box
da---l          25/08/24   8:38 PM                05.Code_IAC_Grafana
-a---l          20/08/24   1:45 PM            493 .gitignore
-a---l          20/08/24   4:54 PM           1589 main.tf

# Now, run the following command.
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
| SonarQube | Ubuntu 24 LTS |
| Nexus | Ubuntu 24 LTS |
| Terraform | Ubuntu 24 LTS |
| Grafana | Ubuntu 24 LTS |

> * Password for the **root** account on all these virtual machines is **xxxxxxx**
>     
> * Perform all the commands as root user unless otherwise specified
>     

### Change the hostname: (optional)

```bash
sudo hostnamectl set-hostname Jenkins
sudo hostnamectl set-hostname SonarQube
sudo hostnamectl set-hostname Nexus
sudo hostnamectl set-hostname Terraform
sudo hostnamectl set-hostname Grafana
```

* Update the /etc/hosts file:
    
    * Open the file with a text editor, for example:
        

```bash
sudo vi /etc/hosts
```

Replace the old hostname with the new one where it appears in the file.

Apply the new hostname without rebooting:

```bash
sudo systemctl restart systemd-logind.service
```

Verify the change:

```bash
hostnamectl
```

Update the package

```bash
sudo -i
apt update 
```

## Setup for SonarQube

```bash
http://publicIPofSonarQube:9000
```

```bash
http://54.87.30.87:9000/
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972737114/13ccafce-2d6d-4f8a-a44a-ac925384ea50.png align="center")

### Default password is admin and you have to change it.

username: admin password: admin

* Create a token in sonarQube
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972759436/dc387dcf-2987-478a-94d5-b8d7aef7b628.png align="center")
    
    ## Setup for Nexus
    
    ```bash
    http://publicIPofNexux:8081
    ```
    
    [http://44.202.10.126:8081/](http://44.202.10.126:8081/)
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972772239/9631895c-9779-4e01-971e-af7c77af8847.png align="center")
    
    Now, we have to click on `sign in`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972783322/62aace3c-c39f-43fd-bbab-1e8ba6883127.png align="center")
    
    We need a password, and we are using Docker. We have to go inside the container in order to get the password, which can be gotten from the container under the directory `/nexus-data/admin.password`
    
    ```bash
    sudo docker exec -it <containerID> /bin/bash
    cat sonatype-work/nexus3/admin.password
    ```
    
    ```bash
    ubuntu@ip-172-31-80-62:~$ docker ps
    CONTAINER ID   IMAGE             COMMAND                  CREATED          STATUS          PORTS                                       NAMES
    e56b0a042dda   sonatype/nexus3   "/opt/sonatype/nexus…"   25 minutes ago   Up 25 minutes   0.0.0.0:8081->8081/tcp, :::8081->8081/tcp   nexus3
    
    ubuntu@ip-172-31-80-62:~$ sudo docker exec -it e56b0a042dda /bin/bash
    bash-4.4$ ls
    nexus  sonatype-work  start-nexus-repository-manager.sh
    bash-4.4$ cd sonatype-work/
    bash-4.4$ ls
    nexus3
    bash-4.4$ cd nexus3/
    bash-4.4$ ls
    admin.password  blobs  cache  db  elasticsearch  etc  generated-bundles  instances  javaprefs  karaf.pid  keystores  lock  log  port  restore-from-backup  tmp
    bash-4.4$ cat admin.password
    4fc19f70-71f5-4e26-901f-198a51e044ba
    bash-4.4$
    ```
    
    Type the new password
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972800826/8054724f-b816-4b5f-8cd5-ce6694702bfa.png align="center")
    
    Select the `enable anonymous access`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972855723/d73be190-5ff0-45e6-963f-aba4098760f8.png align="center")
    
    ## Set up Jenkins
    
    Once Jenkins is setup then install the following plug-in-
    
    * SonarQube Scanner
        
    * Config File Provider
        
    * Maven Integration
        
    * Pipeline: Stage View
        
    * Pipeline Maven Integration
        
    * Kubernetes Client API
        
    * Kubernetes Credentials
        
    * Kubernetes
        
    * Kubernetes CLI
        
    * Docker
        
    * Docker Pipeline
        
    * Eclipse Temurin installer
        
    
    ### Configure the above plug-in
    
    Dashboard &gt; Manage Jenkins&gt; Tools
    

* Configure `Docker`  
    Name: docker  
    install automatically  
    docker version: latest
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972872460/66fc5ad0-ac6b-4f69-bff4-40382f9a104c.png align="center")
    
    Configure `Maven`  
    Name: maven3  
    install automatically
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972883449/780262ec-fd52-4dcf-bf9b-e979a20d6c63.png align="center")
    
    Configure `SonarQube Scanner installations`  
    install automatically
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972896829/a7dab218-1b5d-48f8-be8a-7ddd7980ecfa.png align="center")
    
    `Configure JDK`  
    install automatically  
    version: jdk- 17.0.12+7
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972907101/7aa9e55f-660e-43df-9a88-1f6c42798357.png align="center")
    
    Configure the `SonarQube Server`  
    we will configure the credentials first.  
    Dashboard&gt; Manage Jenkins&gt; Credentials&gt; System &gt; Global credentials(unrestricted)
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972924200/fe97ad4a-5411-449c-be2f-af1ff8213441.png align="center")
    
    Now, we will configure the server
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972936525/04513148-a63e-4d19-ac6f-36ad9f089a7e.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972942425/cd8ea261-5422-4cb7-9ec6-e3dd26b0e38a.png align="center")
    
    Configure the Nexus file
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972952350/5395d922-04d4-45c2-828f-f78a7225ef91.png align="center")
    

### Create a pipeline

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724972979501/c7d4ead3-6734-4a82-b6d2-4d0c8964aada.png align="center")
    
    ```sh
    pipeline {
        agent any
        tools {
            jdk 'jdk17'
            maven 'maven3'
        }
        environment {
            SCANNER_HOME= tool 'sonar-scanner'
        }
    
        stages {
            stage('Git Checkout') {
                steps {
                    git branch: 'main', url: 'https://github.com/mrbalraj007/FullStack-Blogging-App.git'
                }
            }
            stage('Compile') {
                steps {
                    sh "mvn compile"
                }
            }
            stage('Test') {
                steps {
                    sh "mvn test"
                }
            }
            stage('Trivy FS Scan') {
                steps {
                    sh "trivy fs --format table -o fs.html ."
                }
            }
            stage('SonarQube Analysis') {
                steps {
                    withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-App -Dsonar.projectKey=Blogging-App \
                          -Dsonar.java.binaries=target'''
                    }
                }
            }
            stage('Build') {
                steps {
                    sh "mvn package"
                }
            }
            stage('Publish Artifacts') {
                steps {
                    withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                        sh "mvn deploy"  
                  }
                }
            }
        }
    }
    ```
    
    Test the pipeline so far and how it goes.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973003931/8a7ad86f-abb7-4668-93b1-9fb1b922e84b.png align="center")
    
    View from SonarQube:
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973017043/3d493bfa-9482-47a8-bdc1-19f1e4039aee.png align="center")
    
    View from Nexus:
    
    > Now, we will create a private repogitory in Docker hub. My repo name: balrajsi/bloggingapp
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973030386/80e94892-bc68-4998-885a-7105032632eb.png align="center")
    
    Append the existing pipeline and below is the updated pipeline
    
* ```sh
    pipeline {
        agent any
        tools {
            jdk 'jdk17'
            maven 'maven3'
        }
        environment {
            SCANNER_HOME= tool 'sonar-scanner'
        }
    
        stages {
            stage('Clean Workspace'){
                 steps{
                     cleanWs()
                 }
             }
            stage('Git Checkout') {
                steps {
                    git branch: 'main', url: 'https://github.com/mrbalraj007/FullStack-Blogging-App.git'
                }
            }
            stage('Compile') {
                steps {
                    sh "mvn compile"
                }
            }
            stage('Test') {
                steps {
                    sh "mvn test"
                }
            }
            stage('Trivy FS Scan') {
                steps {
                    sh "trivy fs --format table -o fs.html ."
                }
            }
            stage('SonarQube Analysis') {
                steps {
                    withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-App -Dsonar.projectKey=Blogging-App \
                          -Dsonar.java.binaries=target'''
                    }
                }
            }
            stage('Build') {
                steps {
                    sh "mvn package"
                }
            }
            stage('Publish Artifacts') {
                steps {
                    withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                        sh "mvn deploy"  
                  }
                }
            }
            stage('Docker Build and Tag') {
                steps {
                    script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh "docker build -t balrajsi/bloggingapp:latest ."  
                    }
                  }
                }
            }
             stage('Trivy image Scan') {
                steps {
                    sh "trivy image --format table -o image.html balrajsi/bloggingapp:latest"
                }
            }
             stage('Docker Push Image') {
                steps {
                    script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh "docker push balrajsi/bloggingapp:latest"  
                    }
                  }
                }
            }
        }
    }
    ```
    
    ## Now, we will set up an EKS cluster
    
    Once the cluster is set up then we will use the following command to connect it from the Terraform box.
    
    ```bash
    aws eks --region <your region name> update-kubeconfig --name <your clustername>
    ```
    
    ```bash
    aws eks --region us-east-1 update-kubeconfig --name balraj-cluster
    ```
    
    * Check the EKS cluster
        
    
    ```sh
    kubectl get nodes
    ```
    
    ```bash
    ubuntu@ip-172-31-28-76:~/k8s_setup_file$ kubectl get nodes
    NAME                         STATUS   ROLES    AGE   VERSION
    ip-10-0-0-174.ec2.internal   Ready    <none>   13m   v1.30.2-eks-1552ad0
    ip-10-0-0-39.ec2.internal    Ready    <none>   13m   v1.30.2-eks-1552ad0
    ip-10-0-1-198.ec2.internal   Ready    <none>   13m   v1.30.2-eks-1552ad0
    ```
    
    ### Now, we have to do the RBAC configuration for the EKS cluster.
    
    ```css
    comeout from directory "k8s_setup_file" 
    cd ..
    current path
    /home/ubuntu   # Current path
    ```
    
    ### To create a namespace
    
    ```sh
    kubectl create ns webapps
    ```
    
    ### To create a service account
    
    ```sh
    ubuntu@ip-172-31-28-76:~$ cat service.yml
    apiVersion: v1
    kind: ServiceAccount
    metadata:
     name: jenkins
     namespace: webapps
    ```
    
    * command to apply
        
    
    ```sh
    kubectl apply -f service.yml
    ```
    
    #### To test the yml file to see whether it's a valid configuration or not, we can use either `dry-run` or `kubeval`
    
    <details data-node-type="hn-details-summary"><summary>dry-run</summary><div data-type="detailsContent">To dry run kubectl apply --dry-run=client -f role.yaml</div></details>
    
      
    
    <details data-node-type="hn-details-summary"><summary>Kubebal</summary><div data-type="detailsContent"><a target="_blank" rel="noopener noreferrer nofollow" href="https://github.com/instrumenta/kubeval?tab=readme-ov-file" style="pointer-events: none">Kubeval</a> <a target="_blank" rel="noopener noreferrer nofollow" href="https://github.com/instrumenta/kubeval?tab=readme-ov-file" style="pointer-events: none">is a to</a>ol specifically designed to validate Kubernetes YAML files against the Kubernetes OpenAPI schemas.</div></details>
    
    Download [the la](https://github.com/instrumenta/kubeval?tab=readme-ov-file)test release:
    
    ```bash
    wget https://github.com/instrumenta/kubeval/releases/latest/download/kubeval-linux-amd64.tar.gz
    ```
    
    Extract [the arc](https://github.com/instrumenta/kubeval?tab=readme-ov-file)hive:
    
    ```bash
    tar xf kubeval-linux-amd64.tar.gz
    ```
    
    Move the [binary](https://github.com/instrumenta/kubeval?tab=readme-ov-file) to a directory in your PATH:
    
    ```bash
    sudo mv kubeval /usr/local/bin/
    ```
    
    Verify t[he inst](https://github.com/instrumenta/kubeval?tab=readme-ov-file)allation:
    
    ```bash
    kubeval --version
    ```
    
    ### To create a role
    
    ```css
    ubuntu@ip-172-31-28-76:~$ cat roles.yml
    apiVersion: rbac.authorization.k8s.io/v1
    kind: Role
    metadata:
      name: app-role
      namespace: webapps
    rules:
    - apiGroups:
      - ""
      - apps
      - extensions
      - batch
      - autoscaling
      resources:
      - pods
      - secrets
      - services
      - deployments
      - replicasets
      - replicationcontrollers
      - componentstatuses
      - configmaps
      - daemonsets
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - serviceaccounts
      verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
    ```
    
    ### To bind the role to service account
    
    ```css
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: app-rolebinding
      namespace: webapps
    roleRef:
      apiGroup: rbac.authorization.k8s.io
      kind: Role
      name: app-role
    subjects:
    - kind: ServiceAccount
      name: jenkins
      namespace: webapps
    ```
    
    ### To create a token for service account
    
    file name: jen-secret.yml
    
    ```sh
    apiVersion: v1
    kind: Secret
    type: kubernetes.io/service-account-token
    metadata:
      name: mysecretname
      annotations:
       kubernetes.io/service-account.name: jenkins    # your service account name
    ```
    
    To apply to token you need to run with namespace as below
    
    ```sh
    kubectl apply -f jen-secret.yml -n webapps
    ```
    
    Now, we need to create a `docker secret` as we are using `private repo` in docker hub.
    
    ```bash
    kubectl create secret docker-registry regcred \
    --docker-server=https://index.docker.io/v1/ \
    --docker-username=<username> \
    --docker-password=<your_password> \
    --namespace=webapps
    ```
    
    ```bash
     kubectl get secrets -n webapps
    NAME           TYPE                                  DATA   AGE
    mysecretname   kubernetes.io/service-account-token   3      8m1s
    regcred        kubernetes.io/dockerconfigjson        1      60s
    ```
    
    Now, we will get a secret password
    
    ```bash
    kubectl describe secret mysecretname -n webapps
    ```
    
    ```bash
    ubuntu@ip-172-31-28-76:~$ kubectl describe secret mysecretname -n webapps
    Name:         mysecretname
    Namespace:    webapps
    Labels:       <none>
    Annotations:  kubernetes.io/service-account.name: jenkins
                  kubernetes.io/service-account.uid: 234ebef6-6c09-414d-aa04-51ec4509a9cc
    
    Type:  kubernetes.io/service-account-token
    
    Data
    ====
    ca.crt:     1107 bytes
    namespace:  7 bytes
    token:      eyJhbGciOiJSUzI1NiIsImtpZCI6ImpRc1hZU01Meko5VXRUTE1HY2RpdFA4eTNjeUdQQkVOdXBsYmx3ZDZPLWMifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJ3ZWJhcHBzIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6Im15c2VjcmV0bmFtZSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJqZW5raW5zIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMjM0ZWJlZjYtNmMwOS00MTRkLWFhMDQtNTFlYzQ1MDlhOWNjIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50OndlYmFwcHM6amVua2lucyJ9.RFpA3VrD1hw2V8qGmMtLaRwMdlSxWxTYG2t8_NLueOKQIiIivpiRTLqEEcqgUNeCA5sKfK0qg0TeXz4h_b_8GkgtH-tGFVDzfXZ9XtkbFNgUp5HCdnMh_XKrY3HRhDwnBpzDPW0QkDofmmwXzJBUgv0FgD_MO-3kxUBp8fbEa5Tjtl6LXCzLtviLDyTSfubWgsoYff7GUOHAkb1lWw7yhAV-dvSj54iqmb2WqqGMFtkZeDi9Gz8q2IVN9I8txhYoAbB2bQBPZmETXFgGQzf9PQi-BhbPQ2VdSSJ4aPo-FpNVA9y-7JizSgakOYeJ4KnIbcIV1cblXrqX7yIXxuJs6A
    ```
    
    Now, go to Jenkins and create a secret for k8s
    
    ```bash
    Dashboard > Manage Jenkins > Credentials > System > Global credentials (unrestricted)
    ```
    
    Create a `secret text`credentials for K8s.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973227664/aaaa0811-19c7-454f-919f-d8d96ce5cd28.png align="center")
    
    make sure, `Kubectl` is installed on `Jenkins`, if not then use the following command to install it.
    
    ```bash
    sudo snap install kubectl --classic
    ```
    
    Apend in the pipeline as below
    
    ```sh
    pipeline {
        agent any
        tools {
            jdk 'jdk17'
            maven 'maven3'
        }
        environment {
            SCANNER_HOME= tool 'sonar-scanner'
        }
    
        stages {
            stage('Clean Workspace'){
                 steps{
                     cleanWs()
                 }
             }
            stage('Git Checkout') {
                steps {
                    git branch: 'main', url: 'https://github.com/mrbalraj007/FullStack-Blogging-App.git'
                }
            }
            stage('Compile') {
                steps {
                    sh "mvn compile"
                }
            }
            stage('Test') {
                steps {
                    sh "mvn test"
                }
            }
            stage('Trivy FS Scan') {
                steps {
                    sh "trivy fs --format table -o fs.html ."
                }
            }
            stage('SonarQube Analysis') {
                steps {
                    withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-App -Dsonar.projectKey=Blogging-App \
                          -Dsonar.java.binaries=target'''
                    }
                }
            }
            stage('Build') {
                steps {
                    sh "mvn package"
                }
            }
            stage('Publish Artifacts') {
                steps {
                    withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                        sh "mvn deploy"  
                  }
                }
            }
            stage('Docker Build and Tag') {
                steps {
                    script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh "docker build -t balrajsi/bloggingapp:latest ."  
                    }
                  }
                }
            }
             stage('Trivy image Scan') {
                steps {
                    sh "trivy image --format table -o image.html balrajsi/bloggingapp:latest"
                }
            }
             stage('Docker Push Image') {
                steps {
                    script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh "docker push balrajsi/bloggingapp:latest"  
                    }
                  }
                }
            }
            stage('K8s-Deployment') {
                steps {
                    withKubeConfig(caCertificate: '', clusterName: 'balraj-cluster', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://DDC0F8028C6233417C293B1185142548.gr7.us-east-1.eks.amazonaws.com') {
                       sh "kubectl apply -f deployment-service.yml"
                       sleep 30
                     }
                }
            }
            stage('Verify the Deployment') {
                steps {
                    withKubeConfig(caCertificate: '', clusterName: 'balraj-cluster', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://DDC0F8028C6233417C293B1185142548.gr7.us-east-1.eks.amazonaws.com') {
                       sh "kubectl get pods"
                       sh "kubectl get svc"
                     }
                }
            }
            
            
        }
    }
    ```
    
    Run the pipeline
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973245649/5f9e7110-3536-404d-9bbf-0a5481c8a89a.png align="center")
    
    ## Add the email notification.
    
    will add the following text in the pipeline.
    
    ```bash
    post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: "UNKNOWN"
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'
                def body = """
                    <html>
                        <body>
                            <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                                <h2>${jobName} - Build ${buildNumber}</h2>
                                <div style="background-color: ${bannerColor}; padding: 10px;">
                                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                                </div>
                                <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                            </div>
                        </body>
                    </html>
                """
                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    to: "raj10ace@gmail.com",   # Type your email ID
                    from: "jenkins@example.com",
                    replyTo: "jenkins@example.com",
                    mimeType: 'text/html'
                )
            }
        }
    }
    ```
    
    By using this Url you need to generate a app password
    
    ```bash
    https://myaccount.google.com/apppasswords
    ```
    
    Configure email notification in Jenkins. Dashboard Manage Jenkins System
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973267805/92dabb71-04b9-4003-aaa6-88595997a3ca.png align="center")
    
    click on `test configuration` now, configure `Extended E-mail Notification`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973283203/403d7f85-275a-4243-b972-08e6ded403e5.png align="center")
    
    here is the complete pipeline
    
    ```sh
    pipeline {
        agent any
        tools {
            jdk 'jdk17'
            maven 'maven3'
        }
        environment {
            SCANNER_HOME= tool 'sonar-scanner'
        }
    
        stages {
            stage('Clean Workspace'){
                 steps{
                     cleanWs()
                 }
             }
            stage('Git Checkout') {
                steps {
                    git branch: 'main', url: 'https://github.com/mrbalraj007/FullStack-Blogging-App.git'
                }
            }
            stage('Compile') {
                steps {
                    sh "mvn compile"
                }
            }
            stage('Test') {
                steps {
                    sh "mvn test"
                }
            }
            stage('Trivy FS Scan') {
                steps {
                    sh "trivy fs --format table -o fs.html ."
                }
            }
            stage('SonarQube Analysis') {
                steps {
                    withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-App -Dsonar.projectKey=Blogging-App \
                          -Dsonar.java.binaries=target'''
                    }
                }
            }
            stage('Build') {
                steps {
                    sh "mvn package"
                }
            }
            stage('Publish Artifacts') {
                steps {
                    withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                        sh "mvn deploy"  
                  }
                }
            }
            stage('Docker Build and Tag') {
                steps {
                    script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh "docker build -t balrajsi/bloggingapp:latest ."  
                    }
                  }
                }
            }
             stage('Trivy image Scan') {
                steps {
                    sh "trivy image --format table -o image.html balrajsi/bloggingapp:latest"
                }
            }
             stage('Docker Push Image') {
                steps {
                    script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                    sh "docker push balrajsi/bloggingapp:latest"  
                    }
                  }
                }
            }
            stage('K8s-Deployment') {
                steps {
                    withKubeConfig(caCertificate: '', clusterName: 'balraj-cluster', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://DDC0F8028C6233417C293B1185142548.gr7.us-east-1.eks.amazonaws.com') {
                       sh "kubectl apply -f deployment-service.yml"
                       sleep 30
                     }
                }
            }
            stage('Verify the Deployment') {
                steps {
                    withKubeConfig(caCertificate: '', clusterName: 'balraj-cluster', contextName: '', credentialsId: 'k8-cred', namespace: 'webapps', restrictKubeConfigAccess: false, serverUrl: 'https://DDC0F8028C6233417C293B1185142548.gr7.us-east-1.eks.amazonaws.com') {
                       sh "kubectl get pods"
                       sh "kubectl get svc"
                     }
                }
            }
            
            
        }
        post {
        always {
            script {
                def jobName = env.JOB_NAME
                def buildNumber = env.BUILD_NUMBER
                def pipelineStatus = currentBuild.result ?: "UNKNOWN"
                def bannerColor = pipelineStatus.toUpperCase() == 'SUCCESS' ? 'green' : 'red'
                def body = """
                    <html>
                        <body>
                            <div style="border: 4px solid ${bannerColor}; padding: 10px;">
                                <h2>${jobName} - Build ${buildNumber}</h2>
                                <div style="background-color: ${bannerColor}; padding: 10px;">
                                    <h3 style="color: white;">Pipeline Status: ${pipelineStatus.toUpperCase()}</h3>
                                </div>
                                <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                            </div>
                        </body>
                    </html>
                """
                emailext (
                    subject: "${jobName} - Build ${buildNumber} - ${pipelineStatus.toUpperCase()}",
                    body: body,
                    to: "raj10ace@gmail.com",   
                    from: "jenkins@example.com",
                    replyTo: "jenkins@example.com",
                    mimeType: 'text/html'
                )
            }
        }
      }
    }
    ```
    
    E-mail notification
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973303592/db0108bd-a2e1-48a7-a363-fcdb1896eda0.png align="center")
    
    Pipeline view:
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973313795/45e1c2d8-dea3-4aed-b952-80c4b34332d4.png align="center")
    
    We will try to access LB and see deployment is successful or not.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973325948/045afdd9-e641-4b8a-ba6a-7d92a2c16984.png align="center")
    
    application is accessible now.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973358746/042407b7-8a67-44da-934d-54314798a8d4.png align="center")
    
* will create a temp user and password and login with that user credential
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973367479/7c523bd9-ef71-42d2-9892-924f0b80f9fa.png align="center")
    
    clink on `add post`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973378002/e7ded315-18f6-450a-b277-651d26bea52b.png align="center")
    
    # Custom Domain (Optional)
    
    # Configuring monitoring(Grafana).
    
    ```bash
    http://44.201.170.60:3000/login
    ```
    
    default login password for Grafana is `admin/admin`.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973393338/764839c6-43b9-4198-b19c-be2eba2059a1.png align="center")
    
    Now, run the following command for Prometheus
    
    ```bash
    ubuntu@ip-172-31-80-196:~$ cd prometheus/
    ubuntu@ip-172-31-80-196:~/prometheus$ ls
    LICENSE  NOTICE  console_libraries  consoles  prometheus  prometheus.yml  promtool
    ubuntu@ip-172-31-80-196:~/prometheus$ ./prometheus &
    ```
    
    Tyr to open [http://44.201.170.60:9090/](http://44.201.170.60:9090/)
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973421169/605b1fe8-de73-4c9a-aa8f-581ca8d9ac70.png align="center")
    
    Now, run the following command for blackbox
    
    ```bash
    ubuntu@ip-172-31-80-196:~$ ls
    blackbox  prometheus
    
    ubuntu@ip-172-31-80-196:~$ pwd
    /home/ubuntu
    
    ubuntu@ip-172-31-80-196:~$ cd blackbox/
    
    ubuntu@ip-172-31-80-196:~/blackbox$ ls
    LICENSE  NOTICE  blackbox.yml  blackbox_exporter
    
    ubuntu@ip-172-31-80-196:~/blackbox$ ./blackbox_exporter &
    ```
    
    Tyr to open in the browser and you will see below.
    
    [http://44.201.170.60:9115/](http://44.201.170.60:9115/)
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973435998/e18957be-add2-427c-a96d-cf654d1e9306.png align="center")
    
    Open this repo for [blackbox\_exporter](https://github.com/prometheus/blackbox_exporter)
    
    [Need to add the](https://github.com/prometheus/blackbox_exporter) following config file [in `prometheus.ym`](https://github.com/prometheus/blackbox_exporter)`l`
    
    ```bash
     - job_name: 'blackbox'
        metrics_path: /probe
        params:
          module: [http_2xx]  # Look for a HTTP 200 response.
        static_configs:
          - targets:
            - http://prometheus.io    # Target to probe with http.
            - https://prometheus.io   # Target to probe with https.
            - http://example.com:8080 # Target to probe with http on port 8080.
        relabel_configs:
          - source_labels: [__address__]
            target_label: __param_target
          - source_labels: [__param_target]
            target_label: instance
          - target_label: __address__
            replacement: 127.0.0.1:9115
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1724973457003/60f050ac-bee7-469f-93f5-6abb3726a41d.png align="center")
    
    ## Verifying the cluster
    
    ```bash
    kubectl cluster-info
    kubectl get nodes
    ```
    
    ## To destroy the setup using Terraform.
    
    * First go to your `Terrform` EC2 VM and delete the EKS cluster
        
    
    ```bash
    terraform destroy --auto-approve
    ```
    
    * then go to your directory on main folder `"Terraform_Code"` directory then run the command
        
    
    ```bash
    terraform destroy --auto-approve
    ```
    
    ## Conclusion
    
    * **In this blog, we’ve covered the essential steps for deploying and monitoring a Kubernetes application using Jenkins and various supporting tools. By following this guide, you can set up a robust pipeline, ensure your application is deployed smoothly, and monitor its performance effectively.**
        
    
    **Key Takeaways**:
    
    * Set up and configure an EKS cluster and kubectl.
        
    * Define and execute Jenkins pipelines for deployment.
        
    * Configure email notifications for deployment status.
        
    * Set up and test custom domain mapping.
        
    * Install and configure monitoring tools like Prometheus, Blackbox Exporter, and Grafana\*.
        
    
* **Ref Link**:
    
    [1\. CICD Pipeline Project](https://www.youtube.com/watch?v=kWON8yc6efU&list=PLJcpyd04zn7rZtWrpoLrnzuDZ2zjmsMjz&index=73)
    
    [2\. Send Email Notifications from Jenkins | Jenkins](https://www.youtube.com/watch?v=Vwo8zV8zmQU&list=PLJcpyd04zn7rZtWrpoLrnzuDZ2zjmsMjz&index=71)
    
    [3\. Sign in with app passwords](https://support.google.com/mail/answer/185833?hl=en)
    
    [4\. Download Prometheus](https://prometheus.io/download/)
    
    [5.YouTube Link - Production Level CICD Pipeline Project](https://www.youtube.com/watch?v=kWON8yc6efU)