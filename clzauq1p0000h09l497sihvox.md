---
title: "Deploying a YouTube Clone App with DevSecOps & DevOps tools such as Jenkins using Shared Library, Docker, and Kubernetes requires a step-by-step guide"
datePublished: Thu Aug 01 2024 05:44:53 GMT+0000 (Coordinated Universal Time)
cuid: clzauq1p0000h09l497sihvox
slug: deploying-a-youtube-clone-app-with-devsecops-devops-tools-such-as-jenkins-using-shared-library-docker-and-kubernetes-requires-a-step-by-step-guide
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1722487298610/bf7ec23e-c9af-4843-9606-0726321ef173.png
tags: docker, deployment-automation, sonarqube, kubernetes, slack, containers, terraform, jenkins, grafana, docker-images, jenkins-devops, trivy, jenkins-pipeline, grafana-monitoring

---

This blog will help you create a secure DevSecOps pipeline for your project. Using tools like Kubernetes, Docker, SonarQube, Trivy, OWASP Dependency Check, Prometheus, Grafana, Jenkins (with a shared library), Splunk, Rapid API, and Slack notifications, we make it easy to create and manage your environment.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722487140670/b0983989-e6fa-448f-a5da-e0835de28100.png align="center")

**<mark>Environment Setup:</mark>**

Step 1: Launch an Ubuntu instance for

* Jenkins( will install Trivy, Docker and SonarQube)
    
* Splunk
    
* K8S-Master
    
* K8S-Worker
    

I am using Terraform to create a infrastrucutre. I have prepared the Terraform code.

* clone the Terraform git [repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/06.Real-Time-DevOps-Project/Terraform_Code) in your system.
    
* Do the `ls` in a terminal, go to `Terraform_code` Folder, and initiate the following Terraform commands to run the infrastructure.
    

```bash
$ cd Terraform_Code
$ ls -l
total 20
drwxr-xr-x 1 bsingh 1049089    0 Jul 28 12:45 Code_IAC_Jenkins_Trivy_Docker/
drwxr-xr-x 1 bsingh 1049089    0 Jul 28 12:47 Code_IAC_Splunk/
-rw-r--r-- 1 bsingh 1049089  632 Jul 28 12:46 main.tf
```

Now, we have to run the following command

```bash
$ Terraform init
$ Terraform fmt  # for formatting
$ Terraform validate # for validate the codes
$ Terraform plan # for plan the Terraform
$ Terraform apply # to Apply the terraform code.
```

***Note:*** Once you apply the Terraform code, please wait 5 minutes for both instances to be ready and then configure them as described below. Now, we will configure Jenkins.

* To unlock the setup password
    

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484446743/e06b9bf4-498b-46e2-a854-a7eb33be6856.png align="left")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484465086/c1f2db05-70ee-407c-a8a3-441815cececc.png align="center")

* Dashboard for Jenkins
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484492418/0d4801c2-720f-4332-a045-612b284ff7f5.png align="center")
    
    Now, we will try to access the SonarQube as it is accessible via `9000`, Make sure add 9000 ports in the security group.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484551022/2a384d4c-3ef3-4959-91b8-f59dccf7e687.png align="center")
    

```bash
<ec2-public-ip:9000>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484532368/2a49a7a0-723a-4d93-8d38-7ddccc92f1ca.png align="center")

Dashboard of SonarQube

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484574086/c3976a4a-7c36-401d-8f04-0669e32e196f.png align="center")

### Verify the Trivy version on Jenkins machine

```bash
$ trivy --version
Version: 0.53.0
```

### Configure Splunk

Do the following in a terminal: go to the Terraform\_Code folder and run these Terraform commands to set up the infrastructure.

```sh
$ cd Terraform_Code
$ ls -l
total 20
drwxr-xr-x 1 bsingh 1049089    0 Jul 28 12:45 Code_IAC_Jenkins_Trivy_Docker/
drwxr-xr-x 1 bsingh 1049089    0 Jul 28 12:47 Code_IAC_Splunk/
-rw-r--r-- 1 bsingh 1049089  632 Jul 28 12:46 main.tf
```

Now, run the following commands:

```sh
$ terraform init
$ terraform fmt  # for formatting
$ terraform validate # to validate the code
$ terraform plan # to plan the Terraform
$ terraform apply # to apply the Terraform code
```

Note: Once you apply the Terraform code, please wait 5 minutes for both instances to be ready, then configure them as described below. Now, we will configure Jenkins.

To unlock the setup password:

```sh
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Dashboard for Jenkins

Now, we will try to access SonarQube, which is available on port 9000. Make sure to add port 9000 to the security group.

```markdown
<ec2-public-ip:9000>
```

Dashboard of SonarQube

Verify the Trivy version on the Jenkins machine:

```sh
$ trivy --version
Version: 0.53.0
```

Configure Splunk

Verify the public IP address of Splunk and open it in a browser.

```markdown
<splunk-public-ip:8000>
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484641178/0083618a-36a0-4f72-8d7e-676686f2a4fb.png align="center")

Log in with the username and password you created when setting up Splunk.

Dashboard for Splunk

* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484671773/173a0782-d192-48e1-b188-2bf252d040c0.png align="center")
    
* Install the Splunk app for Jenkins; ensure you have Splunk web portal login credentials, or create them first if you don't.
    
* In Splunk Dashboard &gt; Click on Apps &gt; Find more apps
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484703694/7898abef-ebd1-4ccb-ac0d-05b020e9b664.png align="center")
    
    Search for Jenkins in the search bar. When you see the Splunk app for Jenkins, click on Install.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484800055/dd504c67-bcdf-4709-a5cc-8ab11cc926ce.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484806401/f3dc9983-2913-4cf8-8430-9f00864985fc.png align="center")
    
    Click on `go home`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484817859/36874523-f6c6-4146-b010-9a298991c08c.png align="center")
    
    * On the Splunk homepage, you will see that Jenkins has been added.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484828833/f8c138f8-abef-4ae8-84d6-e7c2854a98cc.png align="center")
    
    In the Splunk web interface, go to Settings &gt; Data Inputs.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484865613/e4b35d79-ebba-4ba5-af48-f017afa6bdcd.png align="center")
    
* Click on HTTP Event Collector and Click on Global Settings
    
    Set All tokens to enabled &gt; Uncheck SSL enable &gt; Use 8088 port and click on save
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484880179/fb7cd158-3e52-4c0f-b057-e59a940444e4.png align="center")
    
    Now click on New token
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484890105/7196e239-6fde-4a39-b947-b9338654673c.png align="center")
    
    Provide a Name and click on the next &gt; Review &gt; Click Submit
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484899238/02f828de-ae34-4779-b1d7-2c0c72027565.png align="center")
    
    Click Start searching&gt; Now let’s copy our token again&gt; In the Splunk web interface, go to Settings &gt; Data Inputs&gt; Click on the HTTP event collector &gt;Now copy your token and keep it safe.
    
    * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484920088/f9c5fada-217e-4fdf-88bf-e9f989ec690e.png align="center")
        
    * Add Splunk Plugin in Jenkins  
        Go to Jenkins dashboard &gt; Click on Manage Jenkins &gt; Plugins &gt; Available plugins &gt; Search for Splunk and install it.
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484927293/4fea1002-7a8a-4797-9910-9836280bda46.png align="center")
    
    Now, Click on Manage Jenkins
    
    > System &gt; Go to Splunk &gt; Check to enable &gt; HTTP input host as SPLUNK PUBLIC IP &gt; HTTP token that you generated in Splunk&gt; Jenkins IP and apply.
    > 
    > Don't forget to tick on Enable checkbox.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722484950733/a23ccfdf-07f6-4d8b-9f48-840d68759f0d.png align="center")
    
    If the connection fails, follow the steps below on the Splunk EC2 machine.
    
* ```http
      ubuntu@ip-172-31-23-110:~$ sudo ufw allow 8088
      Rules updated
      Rules updated (v6)
      
      ubuntu@ip-172-31-23-110:~$ sudo ufw status
      Status: inactive
      
      ubuntu@ip-172-31-23-110:~$ sudo ufw allow openSSH
      Rules updated
      Rules updated (v6)
      
      ubuntu@ip-172-31-23-110:~$ sudo ufw allow 8000
      Rules updated
      Rules updated (v6)
      
      ubuntu@ip-172-31-23-110:~$ sudo ufw status
      Status: inactive
      
      ubuntu@ip-172-31-23-110:~$ sudo ufw enable
      Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
      Firewall is active and enabled on system startup
      ubuntu@ip-172-31-23-110:~$
    ```
    

Check Network Connectivity from Jenkins to Splunk

```sh
ping 52.204.146.179
telnet 52.204.146.179 8088
```

### Restart Splunk and Jenkins services to make it effective.

Procedure to restart *Jenkins*

> Jenkins Public IP Address:8080/restart
> 
> ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485121297/ac3642f3-cdd9-4409-a9b6-2f2ce022fdd5.png align="center")

Procedure to restart *Splunk*  
Click on Settings &gt; Server controls &gt; Restart splunk.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485137858/0a40ea3d-087b-42cd-8dff-040773b5b2ee.png align="center")

### On the Jenkins Server, we will create a simple hello pipeline to check if the logs are visible in Splunk.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485151137/f1b892ca-360d-4de7-a62a-d174651db4e5.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485167332/4f86594a-387a-4139-97f0-ba0d635fda61.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485171772/f342d01b-bcc1-4d81-b6fe-0a1402441e97.png align="center")

Now go to Splunk, click on the Jenkins app, and you will see some data from Jenkins.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485184036/7b40a7c1-b63c-4b14-93f5-650c8e48b56b.png align="center")

We can see the logs in Splunk. 😃

If you want to see the more details logs then switch it to `admin` and you will see below

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485206904/6c87788f-d3d9-4586-b0a5-58ba656fd6c9.png align="center")

## Integrate Slack for Notifications

If you don't have a Slack account, create one first. If you already have an account, log in. [Slack login](https://slack.com/signin#/signin)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485239313/036e4641-6a59-4bb6-b4ab-4462ebe5086f.png align="center")

Create a Slack account and create a channel Named "Jenkins\_Notification"

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485249883/53cf1ada-c5be-4314-966c-8a78e804a2cc.png align="center")

* ### *Install the Jenkins CI app on Slack*
    
* Go to Slack and click on your name &gt; Select Settings and Administration &gt; Click on Manage apps
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485285704/4187e7bc-0bd1-4ca9-9508-c1ea6ab8d765.png align="center")
    
    search here "`Jenkins CI`" &gt; Click on `Add to Slack`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485300405/e1837fb4-ee48-4fce-967a-f08027154825.png align="center")
    
    Select the change name "Jenkins" and click on `add Jenkins CI integration`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485312795/d00441f2-3b8a-4358-a770-74c3ffc09a89.png align="center")
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485315477/8a078384-7159-4eab-9059-da1be2d68008.png align="center")

You will be sent to this page

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485335980/b517f6e8-5791-4517-a918-0b2791705f92.png align="center")

### Install Slack Notification Plugin in Jenkins

Go to Jenkins Dashboard &gt; Click on manage Jenkins &gt; Plugins &gt; Available plugins "Search for Slack Notification and install"

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485359195/f3b3fe3a-16fb-4541-b9dd-5479fef52c6a.png align="center")

Now, we will be configure the credential

> Click on Manage Jenkins –&gt; Credentials &gt; Global &gt; Select kind as Secret Text &gt; At Secret Section Provide Your Slack integration token credential ID&gt; Id and description are optional and create
> 
> ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485374890/a5674253-6e48-4bfc-a8c2-dda712097a9d.png align="center")
> 
> in Slack Step 3 it is mention the token.
> 
> ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485387917/72fe5b5c-b3f2-47cc-98d7-3f9773e2420f.png align="center")
> 
> Click on Manage Jenkins &gt; System &gt; Go to the end of the page &gt; Workspace &gt; team subdomain &gt; Credential –&gt; Select your Credential for Slack &gt; Default channel –&gt; Provide your Channel name &gt; Test connection &gt; Click on Apply and save
> 
> ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485410068/504a9235-dcbb-4dd6-83de-d1b42cd56fba.png align="center")
> 
> You will get a notification as below on Slack app.
> 
> ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485422994/cdb9f70e-147e-4f08-acd1-59702f4a5581.png align="center")
> 
> Add this to the pipeline
> 
> ```bash
> def COLOR_MAP = [
>     'FAILURE' : 'danger',
>     'SUCCESS' : 'good'
> ]
> post {
>     always {
>         echo 'Slack Notifications'
>         slackSend (
>             channel: '#jenkins',   #change your channel name
>             color: COLOR_MAP[currentBuild.currentResult],
>             message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
>         )
>     }
> }
> ```
> 
> If you don't know how to do [Integrating Slack with Jenkins](https://www.youtube.com/watch?v=9ZUy3oHNgh8&t=0s)

* Sample pipeline with post action.
    
* ```bash
      def COLOR_MAP = [
          'FAILURE' : 'danger',
          'SUCCESS' : 'good'
      ]
      
      
      pipeline {
          agent any
      
          stages {
              stage('Hello') {
                  steps {
                      echo 'Hello World'
                  }
              }
          }
      
      
      
      post {
      always {
          echo 'Slack Notifications'
          slackSend (
              channel: '#jenkins',
              color: COLOR_MAP[currentBuild.currentResult],
              message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
          )
      }
      }
      }
    ```
    
    You will get Slack Notification
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485569206/0771003c-2c2f-407a-9762-4395b3f693ff.png align="center")
    
* Splunk Notification
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485580921/fda146b8-8250-4d91-b385-b6b8fb4684bd.png align="center")
    
    ### Start Jenkins Shared library Job : [Ref link](https://www.jenkins.io/doc/book/pipeline/shared-libraries/)
    
    Go to Jenkins dashboard and click on New Item. Provide a name for the Job & click on Pipeline and click on OK.
    
    2.b: Create a `Jenkins shared library1` in GitHub Create a new repository in GitHub named `Jenkins_shared_library1`
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485636264/30c09949-d384-4735-b6ca-b218f3928316.png align="center")
    
    * Connect to your VS Code , Create a directory named `Jenkins_shared_library1`, Create a Vars directory inside it
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485649885/c718914b-37eb-43c0-ba9a-506d8d7dd68d.png align="center")
    
    Open Terminal Run the below commands to push to GitHub
    
* ```sh
      echo "Welcome to my Jenkins_shared_library" >> README.md
      git init
      git add README.md
      git commit -m "first commit"
      git branch -M main
      git push -u origin main
    ```
    
    * Create `cleanWorkspace.groovy` file and add the below code
        
    
    ```sh
    //cleanWorkspace.groovy //cleans workspace
    def call() {
        cleanWs()
    }
    ```
    
    * Create `checkoutGit.groovy` file and add the code below.
        
    * *Note:* we will be using this [`Youtube-clone-app`](https://github.com/mrbalraj007/Youtube-clone-app) repo.
        
    
    ```sh
    def call(String gitUrl, String gitBranch) {
        checkout([
            $class: 'GitSCM',
            branches: [[name: gitBranch]],
            userRemoteConfigs: [[url: gitUrl]]
        ])
    }
    ```
    
    Now push them to GitHub using the below commands from vs code
    
    ```sh
    git add .
    git commit -m "message"
    git push origin main
    ```
    
    * Add Jenkins shared library to Jenkins system Go to Jenkins Dashboard &gt; Click on Manage Jenkins &gt; system &gt; Search for `Global Trusted Pipeline Libraries` and click on Add
        
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485735498/b72a15fe-1bfe-4dab-871e-46b864c6e66b.png align="center")
    
    Now Provide a name that we have to call in our pipeline
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485749598/f6ac8bfe-3f24-4054-b2eb-b936bddc32ee.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485753591/91452e1a-dcf5-4ab7-9916-164437bb49e8.png align="center")
    
    Click apply and save
    
    Now, go to the your pipeline
    
    * Run Pipeline Go to Jenkins Dashboard again & select the job and add the below pipeline
        
    
    ```sh
    @Library('Jenkins_shared_library') _  //name used in jenkins system for library
    def COLOR_MAP = [
        'FAILURE' : 'danger',
        'SUCCESS' : 'good'
    ]
    pipeline{
        agent any
        parameters {
            choice(name: 'action', choices: 'create\ndelete', description: 'Select create or destroy.')
        }
        stages{
            stage('clean workspace'){
                steps{
                    cleanWorkspace()
                }
            }
            stage('checkout from Git'){
                steps{
                    checkoutGit('https://github.com/mrbalraj007/Youtube-clone-app.git', 'main')
                }
            }
         }
         post {
             always {
                 echo 'Slack Notifications'
                 slackSend (
                     channel: '#jenkins',   //change your channel name
                     color: COLOR_MAP[currentBuild.currentResult],
                     message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
                   )
               }
           }
       }
    ```
    
    Build with parameters and build
    
    here is the Stage view.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485788158/125f66c7-e128-40ab-b484-f2c85bdba50f.png align="center")
    
    ## Install Plugins like JDK, Sonarqube Scanner, NodeJs
    
    Install Plugin
    
    Go to Manage Jenkins →Plugins → Available Plugins →
    
    Install below plugins
    
    I→ Eclipse Temurin Installer (Install without restart)
    
    II → SonarQube Scanner (Install without restart)
    
    III → NodeJs Plugin (Install Without restart)
    
    ## Configure Java and Nodejs in Global Tool Configuration
    
    Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485821345/1630ef37-5062-42b0-ab80-b4b1aac0caaf.png align="center")
    
    Need to use the latest version of NodeJS
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485831538/5cad0218-4914-4de4-ac13-37da013a4c3c.png align="center")
    
    #### Configure Sonar Server in Manage Jenkins
    
    Grab the Public IP Address of your EC2 Instance. SonarQube works on Port 9000, so use :9000. Go to your SonarQube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485867040/a5a84d9e-dbd5-474e-8a92-56be2d5bf3f0.png align="center")
    
    click on update Token
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485877432/7a9f4064-1277-479c-8c89-a9c55b73c97e.png align="center")
    
    Create a token with a name and generate and notedown somewhere.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485886419/14daa01f-55b3-4d9f-a7aa-ff1876c496aa.png align="center")
    
    Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485899598/eb9dac2a-6b0b-43b4-a2d1-993b56292ce4.png align="center")
    
* Now, go to Dashboard → Manage Jenkins → System and Add like the below image.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485909904/50f4a97a-ffaa-40e5-8ee8-52eb6e067e10.png align="center")
    
    Click on Apply and Save.
    
    The Configure System option is used in Jenkins to configure different servers. Global Tool Configuration is used to configure different tools that we install using plugins. We will install a Sonar scanner in the tools.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485938436/cac3e760-42ee-4e1c-bf0b-2237e1882572.png align="center")
    
    In the Sonarqube Dashboard add a quality gate also Administration–&gt; Configuration–&gt;Webhooks &gt; Click on Create
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485954653/bcd15f38-206d-4843-9429-a46f1c577b94.png align="center")
    
    Add details
    
    ```sh
    #in url section of quality gate
    <http://jenkins-public-ip:8080>/sonarqube-webhook/
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485969663/5132f32a-c9b8-434d-ba42-410aa5acc211.png align="center")
    
    ### Add New stages to the pipeline
    
    Go to vs code and create a file `sonarqubeAnalysis.groovy` & add the below code and push to Jenkins shared library GitHub Repo.
    
    ```sh
    def call() {
        withSonarQubeEnv('sonar-server') {
            sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Youtube -Dsonar.projectKey=Youtube '''
        }
    }
    ```
    
    Create another file for qualityGate.groovy
    
    ```sh
    def call(credentialsId) {
        waitForQualityGate abortPipeline: false, credentialsId: credentialsId
    }
    ```
    
    Create another file for npmInstall.groovy
    
    ```sh
    def call() {
        sh 'npm install'
    }
    ```
    
    Push them to the GitHub Jenkins shared library
    
    ```sh
    git add .
    git commit -m "message"
    git push origin main
    ```
    
    Add these stages to the pipeline now
    
    ```sh
    //under parameters
    tools{
            jdk 'jdk17'
            nodejs 'node16'
        }
        environment {
            SCANNER_HOME=tool 'sonar-scanner'
        }
    // add in stages
    stage('sonarqube Analysis'){
            when { expression { params.action == 'create'}}
                steps{
                    sonarqubeAnalysis()
                }
            }
            stage('sonarqube QualitGate'){
            when { expression { params.action == 'create'}}
                steps{
                    script{
                        def credentialsId = 'Sonar-token'
                        qualityGate(credentialsId)
                    }
                }
            }
            stage('Npm'){
            when { expression { params.action == 'create'}}
                steps{
                    npmInstall()
                }
            }
    ```
    
    Pipeline should be looks like
    
    ```yaml
    @Library('Jenkins_shared_library') _
    def COLOR_MAP = [
        'FAILURE' : 'danger',
        'SUCCESS' : 'good'
    ]
    pipeline{
        agent any
        tools{
            jdk 'jdk17'
            nodejs 'node16'
        }
        environment {
            SCANNER_HOME=tool 'sonar-scanner'
        }
        parameters {
            choice(name: 'action', choices: 'create\ndelete', description: 'Select create or destroy.')
        }
        stages{
            stage('clean workspace'){
                steps{
                    cleanWorkspace()
                }
            }
            stage('checkout from Git'){
                steps{
                    checkoutGit('https://github.com/mrbalraj007/Youtube-clone-app.git', 'main')
                }
            }
            stage('sonarqube Analysis'){
            when { expression { params.action == 'create'}}
                steps{
                    sonarqubeAnalysis()
                }
            }
            stage('sonarqube QualitGate'){
            when { expression { params.action == 'create'}}
                steps{
                    script{
                        def credentialsId = 'Sonar-token'
                        qualityGate(credentialsId)
                    }
                }
            }
            stage('Npm'){
            when { expression { params.action == 'create'}}
                steps{
                    npmInstall()
                }
            }
         }
         post {
             always {
                 echo 'Slack Notifications'
                 slackSend (
                     channel: '#jenkins',
                     color: COLOR_MAP[currentBuild.currentResult],
                     message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
                   )
               }
           }
       }
    ```
    
    Now, time to Build it again and see the Stage View.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722485992548/81f8ca98-1064-4a79-a8c4-1f7a49b9832f.png align="center")
    
    To see the report, you can go to the Sonarqube Server and navigate to Projects.
    
    You can see the report has been generated, and the status shows as passed. You can see that 549 lines were scanned. For a detailed report, you can go to issues.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486018407/a4e49fa4-55bf-410e-a5d2-2396736e8e03.png align="center")
    
    Slack Notification
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486027856/88323dcd-7726-49f7-bdd1-7ea2fb36f3df.png align="center")
    
    ### Install OWASP Dependency Check Plugins
    
    GotoDashboard → Manage Jenkins → Plugins → OWASP Dependency-Check. Click on it and install it without restart.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486041615/c4a6495d-fd56-4429-a362-bd20e99389a1.png align="center")
    
    First, we configured the Plugin and next, we had to configure the Tool Goto Dashboard → Manage Jenkins → Tools
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486054856/7982c18f-23d6-43a2-acfb-f37f3dd047d4.png align="center")
    
    Click on Apply and Save here.
    
    Create a file for trivyFs.groovy
    
    ```sh
    def call() {
        sh 'trivy fs . > trivyfs.txt'
    }
    ```
    
    Push to GitHub
    
    ```sh
    git add .
    git commit -m "message"
    git push origin main
    ```
    
    Add the below stages to the Jenkins pipeline
    
    ```sh
    stage('Trivy file scan'){
            when { expression { params.action == 'create'}}
                steps{
                    trivyFs()
                }
            }
            stage('OWASP FS SCAN') {
            when { expression { params.action == 'create'}}
                steps {
                    dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
    ```
    
    **Note**: The stage with the Dependency Check steps cannot be directly used inside a shared library. The main reason is that pipelines loaded from shared libraries have more restrictive script security by default, so the dependencyCheck and dependencyCheckPublisher steps would fail with rejected signature errors.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486107301/5509e24c-b1a3-4f2b-9e5e-981ec4a65b37.png align="center")
    
    You will see that in status, a graph will also be generated and Vulnerabilities.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486117903/4eaf54a6-fc85-46dc-a6fb-e5436b429566.png align="center")
    
    ### Docker Image Build and Push
    
    We need to install the Docker tool in our system, Goto Dashboard → Manage Plugins → Available plugins → Search for Docker and install these plugins
    
    ```sh
    Docker
    Docker Commons
    Docker Pipeline
    Docker API
    docker-build-step
    ```
    
    and click on install without restart
    
    Add DockerHub Username and Password under Global Credentials
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486131610/351ffe25-ea73-4595-94b2-55cc36e7018e.png align="center")
    
    Now, goto Dashboard → Manage Jenkins → Tools
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486145317/6685d365-afe0-44db-86eb-45edc30eff1f.png align="center")
    
    ### Should have an [**rapidapi**account.](https://rapidapi.com/)
    
    "Once you have an accoun[t, your](https://rapidapi.com/) name will automatically appear in Rapid API."
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486160378/cb9db37c-f569-46e3-97dd-75abac1ac432.png align="center")
    
    In the search bar, type "YouTube" and choose "YouTube v3."
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486172033/8cc40e06-0d31-4bac-b31e-f3ef9d8485c6.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486219553/a4f4ecf3-926b-49c3-a06a-e9cb58ff8b53.png align="center")
    
    Copy API and use it in the groovy file
    
    docker build –build-arg REACT\_APP\_RAPID\_API\_KEY= -t ${imageName} .
    
    Create a shared library file for dockerBuild.groovy
    
    ```sh
    def call(String dockerHubUsername, String imageName) {
        // Build the Docker image
        sh "docker build --build-arg REACT_APP_RAPID_API_KEY=f0ead79813mshb0aa -t ${imageName} ."
         // Tag the Docker image
        sh "docker tag ${imageName} ${dockerHubUsername}/${imageName}:latest"
        // Push the Docker image
        withDockerRegistry([url: 'https://index.docker.io/v1/', credentialsId: 'docker']) {
            sh "docker push ${dockerHubUsername}/${imageName}:latest"
        }
    }
    ```
    
    Create another file for trivyImage.groovy
    
    ```sh
    def call() {
        sh 'trivy image sevenajay/youtube:latest > trivyimage.txt'
    }
    ```
    
    Push the above files to the GitHub shared library.
    
    Add this stage to your pipeline with parameters
    
    ```sh
    #add inside parameter
     string(name: 'DOCKER_HUB_USERNAME', defaultValue: 'balrajsi', description: 'Docker Hub Username')
     string(name: 'IMAGE_NAME', defaultValue: 'youtube', description: 'Docker Image Name')
    #stage
    stage('Docker Build'){
            when { expression { params.action == 'create'}}
                steps{
                    script{
                       def dockerHubUsername = params.DOCKER_HUB_USERNAME
                       def imageName = params.IMAGE_NAME
                       dockerBuild(dockerHubUsername, imageName)
                    }
                }
            }
            stage('Trivy iamge'){
            when { expression { params.action == 'create'}}
                steps{
                    trivyImage()
                }
            }
    ```
    
    ### Run the Docker container
    
    Create a new file runContainer.groovy
    
    ```sh
    def call(){
        sh "docker run -d --name youtube1 -p 3000:3000 balrajsi/youtube:latest"
    }
    ```
    
    Create Another file to remove container removeContainer.groovy
    
    ```sh
    def call(){
        sh 'docker stop youtube1'
        sh 'docker rm youtube1'
    }
    ```
    
    Push them to the Shared library GitHub repo
    
    ```sh
    git add .
    git commit -m "message"
    git push origin main
    ```
    
    Add the below stages to the Pipeline
    
    ```sh
    stage('Run container'){
            when { expression { params.action == 'create'}}
                steps{
                    runContainer()
                }
            }
            stage('Remove container'){
            when { expression { params.action == 'delete'}}
                steps{
                    removeContainer()
                }
            }
    ```
    
    here is the updated pipeline so far
    
    ```sh
    @Library('Jenkins_shared_library') _
    def COLOR_MAP = [
        'FAILURE' : 'danger',
        'SUCCESS' : 'good'
    ]
    pipeline{
        agent any
        tools{
            jdk 'jdk17'
            nodejs 'node16'
        }
        
        environment {
            SCANNER_HOME=tool 'sonar-scanner'
        }
        parameters {
            choice(name: 'action', choices: 'create\ndelete', description: 'Select create or destroy.')
            string(name: 'DOCKER_HUB_USERNAME', defaultValue: 'balrajsi', description: 'Docker Hub Username')
            string(name: 'IMAGE_NAME', defaultValue: 'youtube', description: 'Docker Image Name')
        }
        stages{
            stage('clean workspace'){
                steps{
                    cleanWorkspace()
                }
            }
            stage('checkout from Git'){
                steps{
                    checkoutGit('https://github.com/mrbalraj007/Youtube-clone-app.git', 'main')
                }
            }
            stage('sonarqube Analysis'){
            when { expression { params.action == 'create'}}
                steps{
                    sonarqubeAnalysis()
                }
            }
            stage('sonarqube QualitGate'){
            when { expression { params.action == 'create'}}
                steps{
                    script{
                        def credentialsId = 'Sonar-token'
                        qualityGate(credentialsId)
                    }
                }
            }
            stage('Npm'){
            when { expression { params.action == 'create'}}
                steps{
                    npmInstall()
                }
            }
            stage('Trivy file scan'){
            when { expression { params.action == 'create'}}
                steps{
                    trivyFs()
                }
            }
            stage('OWASP FS SCAN') {
            when { expression { params.action == 'create'}}
                steps {
                    dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
            stage('Docker Build'){
            when { expression { params.action == 'create'}}
                steps{
                    script{
                       def dockerHubUsername = params.DOCKER_HUB_USERNAME
                       def imageName = params.IMAGE_NAME
                       dockerBuild(dockerHubUsername, imageName)
                    }
                }
            }
            stage('Trivy iamge'){
            when { expression { params.action == 'create'}}
                steps{
                    trivyImage()
                }
            }
            stage('Run container'){
            when { expression { params.action == 'create'}}
                steps{
                    runContainer()
                }
            }
            stage('Remove container'){
            when { expression { params.action == 'delete'}}
                steps{
                    removeContainer()
                }
            }
            
         }
         post {
             always {
                 echo 'Slack Notifications'
                 slackSend (
                     channel: '#jenkins',
                     color: COLOR_MAP[currentBuild.currentResult],
                     message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
                   )
               }
           }
       }
    ```
    
    Build with parameters ‘create’
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486245206/da6d6295-0b8e-4769-8b32-d7192f3f63c1.png align="center")
    
    I can see the image pushed to my Docker Hub account.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486249726/db4ae352-0194-4092-97dc-d632bdfd40fb.png align="center")
    
    It will start the container [public-ip-jenkins:3000](public-ip-jenkins:3000)
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486303483/a0b64a00-e887-4293-857f-f3cfc66379fc.png align="center")
    
    Build with parameters ‘delete’ It will stop and remove the Container
    
    Delete view:
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486324906/0d38de88-8c3d-4382-a7aa-1b7dbc012e01.png align="center")
    
    ### Kubernetes Setup
    
    Will create two Ubuntu `t2.large` instances, one for the k8s master and the other for the worker.
    
    Install Kubectl on the Jenkins machine as well.
    
    #### Kubectl is to be installed on Jenkins
    
    Connect to your Jenkins machine, and create a shell script file [install.sh](http://kube.sh)
    
    ```sh
    sudo apt-get update -y
    
    # Install AWS CLI
    sudo apt-get install -y awscli, curl
    
    # Install kubectl
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    kubectl version --client
    ```
    
    #### K8S Master-Worker setup
    
    Will set the host name
    
    ```sh
    # Master Node
    sudo hostnamectl set-hostname K8s-Master
    
    # Worker Node
    sudo hostnamectl set-hostname K8s-Worker
    ```
    
    Will install the package on both Master & Node
    
    ```sh
    sudo apt-get update
    sudo apt-get install -y docker.io
    sudo usermod –aG docker Ubuntu
    newgrp docker
    sudo chmod 777 /var/run/docker.sock
    sudo apt-get update
    # apt-transport-https may be a dummy package; if so, you can skip that package
    sudo apt-get install -y apt-transport-https ca-certificates curl gpg
    curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
    sudo apt-get update
    sudo apt-get install -y kubelet kubeadm kubectl
    sudo apt-mark hold kubelet kubeadm kubectl 
    sudo snap install kube-apiserver
    ```
    
    *Note*\- if ask for password then presh enter and move on.
    
* **Now**, we will run the following code on `Master node only`
    
    ```sh
    sudo kubeadm init --pod-network-cidr=10.244.0.0/16
    # in case your in root exit from it and run below commands
    mkdir -p $HOME/.kube
    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    sudo chown $(id -u):$(id -g) $HOME/.kube/config
    kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
    ```
    
    On Worker Node-:
    
* sudo kubeadm join : --token --discovery-token-ca-cert-hash --v=5
    
    ```sh
    sudo kubeadm join 172.31.20.226:6443 --token 035jqv.haoqokd3166ocy8f         --discovery-token-ca-cert-hash sha256:938fa33276feb05b57db6231f546cea2a4a2b49b7574618737e02982cb7ceb7e --v=5
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486469029/9a965b75-cbd9-4903-adf6-8903dd1d5b65.png align="center")
    
* From the K8S-master download the config file which is under path
    
    ```sh
    /home/ubuntu/.kube
    ```
    
    copy it and save it in documents or another folder, save it as secret-file.txt
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486489287/cd9d86f2-8078-4b71-a30f-0fb680ef3c3f.png align="center")
    
    If you are using Moboxterm then you can follow as below.
    
* ### On Jenkins Server
    
    Install Kubernetes Plugin,
    
    ```sh
    Kubernetes Credentials	
    Kubernetes
    Kubernetes Client API
    Kubernetes CLI
    ```
    
    go to Manage Jenkins -&gt; Manage Credentials -&gt; Click on Jenkins Global -&gt; Add Credentials
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486534359/241c7bb9-e00d-45c7-9ea0-56f0bd7daf0e.png align="center")
    
    ### Install Helm & Monitoring K8S using Prometheus and Grafana
    
    On Kubernetes Master install the helm
    
    ```sh
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486547744/eba77ba8-f200-4015-93fb-8dadbdb7eaf0.png align="center")
    
    See the Helm version
    
    ```sh
    helm version --client
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486569030/875c10b6-f869-4815-8a1a-aa82f84d460f.png align="center")
    
    We need to add the Helm Stable Charts for your local client. Execute the below command:
    
    ```sh
    helm repo add stable https://charts.helm.sh/stable
    ```
    
    Add Prometheus Helm repo
    
    ```sh
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    ```
    
    Create Prometheus namespace
    
    ```sh
    kubectl create namespace prometheus
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486583678/64093fe2-3110-44bf-9e13-4665b134ed7e.png align="center")
    
    Install kube-Prometheus-stack Below is the command to install kube-prometheus-stack. The helm repo kube-stack-Prometheus (formerly Prometheus-operator) comes with a Grafana deployment embedded.
    
    Let’s check if the Prometheus and Grafana pods are running or not
    
    ```sh
    kubectl get pods -n prometheus
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486598003/f2b9f762-902a-4d42-ad95-6d4ff39b1306.png align="center")
    
    Now See the services
    
    ```sh
    kubectl get svc -n prometheus
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486610543/5be253ed-3448-4876-921c-da3d478bbdd3.png align="center")
    
    This confirms that Prometheus and grafana have been installed successfully using Helm.
    
    To make Prometheus and grafana available outside the cluster, use LoadBalancer or NodePort instead of ClusterIP.
    
    Edit Prometheus Service
    
    ```sh
    kubectl edit svc stable-kube-prometheus-sta-prometheus -n prometheus
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486628443/8b968964-39d8-4474-86c9-cea0fcb2f334.png align="center")
    
    Edit Grafana Service
    
    ```sh
    kubectl edit svc stable-grafana -n prometheus
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486643788/1a17bc9e-4725-46c7-9550-eaf9d3877567.png align="center")
    
    Verify if the service is changed to LoadBalancer and also get the Load BalancerPorts.
    
    ```sh
    kubectl get svc -n prometheus
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486655469/d7f6b6f0-5931-46f8-a774-2ed82450b029.png align="center")
    
    Access Grafana UI in the browser
    
    Get the external IP from the above screenshot and put it in the browser [k8s-master-public-ip:external-ip](k8s-master-public-ip:external-ip)
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486666403/e213530f-e8bb-4875-a4be-19d9ac4e573f.png align="center")
    
    Login to Grafana
    
    ```sh
    UserName: admin
    Password: prom-operator
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486674865/627dc2d6-20c4-4fd7-b446-28f020cfd233.png align="center")
    
    * Create a Dashboard in Grafana
        
    
    In Grafana, we can create various kinds of dashboards as per our needs.
    
    How to Create Kubernetes Monitoring Dashboard? For creating a dashboard to monitor the cluster: Click the ‘+’ button on the left panel and select ‘Import’. Enter the `15661` dashboard id under [`Grafana.com`](http://Grafana.com) Dashboard. Click ‘Load’.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486689800/e2eec44a-fc38-44d9-a199-4bcd45993ac5.png align="center")
    
    Select ‘Prometheus’ as the endpoint under the Prometheus data sources drop-down. Click ‘Import’.
    
    This will show the monitoring dashboard for all cluster nodes
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486712686/91f91af7-a919-42bc-9c30-9cc32cdd119c.png align="center")
    
    ### **Create Kubernetes Cluster Monitoring Dashboard.**
    
    For creating a dashboard to monitor the cluster:
    
    Click the ‘+’ button on the left panel and select ‘Import’.
    
    Enter 3119 dashboard ID under [Grafana.com](http://Grafana.com) Dashboard.
    
    Click ‘Load’.
    
    Select ‘Prometheus’ as the endpoint under the Prometheus data sources drop-down.
    
    Click ‘Import’.
    
    This will show the monitoring dashboard for all cluster nodes
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486726878/6dcc5643-cdfc-4cc7-baa5-7d7a9437a2d6.png align="center")
    
    * ### **Create a POD Monitoring Dashboard**
        
    
    For creating a dashboard to monitor the cluster:
    
    Click the ‘+’ button on the left panel and select ‘Import’.
    
    Enter 6417 dashboard ID under [Grafana.com](http://Grafana.com) Dashboard.
    
    Click ‘Load’.
    
    Select ‘Prometheus’ as the endpoint under the Prometheus data sources drop-down.
    
    Click ‘Import’.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486788175/a871e547-4cce-4816-889c-b87d56883b1f.png align="center")
    

## **Kubernetes Deployment**

* Let’s Create a Shared Jenkins library file for K8s deploy and delete
    
    Name `kubeDeploy.groovy`
    
* ```sh
      def call() {
          withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
              sh "kubectl apply -f deployment.yml"
          }
      }
    ```
    
    To delete deployment
    
    Name `kubeDelete.groovy`
    
    ```sh
    def call() {
        withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'k8s', namespace: '', restrictKubeConfigAccess: false, serverUrl: '') {
            sh "kubectl delete -f deployment.yml"
        }
    }
    ```
    
    Let’s push them to GitHub
    
    ```sh
    git add .
    git commit -m "message"
    git push origin main
    ```
    
    The final stage of the Pipeline
    
    ```sh
            stage('Kube deploy'){
            when { expression { params.action == 'create'}}
                steps{
                    kubeDeploy()
                }
            }
            stage('kube deleter'){
            when { expression { params.action == 'delete'}}
                steps{
                    kubeDelete()
                }
            }
    ```
    
    Overall pipeline state
    
    ```sh
    @Library('Jenkins_shared_library') _
    def COLOR_MAP = [
        'FAILURE' : 'danger',
        'SUCCESS' : 'good'
    ]
    pipeline{
        agent any
        tools{
            jdk 'jdk17'
            nodejs 'node16'
        }
        
        environment {
            SCANNER_HOME=tool 'sonar-scanner'
        }
        parameters {
            choice(name: 'action', choices: 'create\ndelete', description: 'Select create or destroy.')
            string(name: 'DOCKER_HUB_USERNAME', defaultValue: 'balrajsi', description: 'Docker Hub Username')
            string(name: 'IMAGE_NAME', defaultValue: 'youtube', description: 'Docker Image Name')
        }
        stages{
            stage('clean workspace'){
                steps{
                    cleanWorkspace()
                }
            }
            stage('checkout from Git'){
                steps{
                    checkoutGit('https://github.com/mrbalraj007/Youtube-clone-app.git', 'main')
                }
            }
            stage('sonarqube Analysis'){
            when { expression { params.action == 'create'}}
                steps{
                    sonarqubeAnalysis()
                }
            }
            stage('sonarqube QualitGate'){
            when { expression { params.action == 'create'}}
                steps{
                    script{
                        def credentialsId = 'Sonar-token'
                        qualityGate(credentialsId)
                    }
                }
            }
            stage('Npm'){
            when { expression { params.action == 'create'}}
                steps{
                    npmInstall()
                }
            }
            stage('Trivy file scan'){
            when { expression { params.action == 'create'}}
                steps{
                    trivyFs()
                }
            }
            stage('OWASP FS SCAN') {
            when { expression { params.action == 'create'}}
                steps {
                    dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                }
            }
            stage('Docker Build'){
            when { expression { params.action == 'create'}}
                steps{
                    script{
                       def dockerHubUsername = params.DOCKER_HUB_USERNAME
                       def imageName = params.IMAGE_NAME
                       dockerBuild(dockerHubUsername, imageName)
                    }
                }
            }
            stage('Trivy iamge'){
            when { expression { params.action == 'create'}}
                steps{
                    trivyImage()
                }
            }
            stage('Run container'){
            when { expression { params.action == 'create'}}
                steps{
                    runContainer()
                }
            }
            stage('Remove container'){
            when { expression { params.action == 'delete'}}
                steps{
                    removeContainer()
                }
            }
            stage('Kube deploy'){
            when { expression { params.action == 'create'}}
                steps{
                    kubeDeploy()
                }
            }
            stage('kube deleter'){
            when { expression { params.action == 'delete'}}
                steps{
                    kubeDelete()
                }
            }
            
         }
         post {
             always {
                 echo 'Slack Notifications'
                 slackSend (
                     channel: '#jenkins',
                     color: COLOR_MAP[currentBuild.currentResult],
                     message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
                   )
               }
           }
       }
    ```
    
    It will apply the deployment
    
    Stage view
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486921071/9779ff62-2acc-465e-b637-b927792f9f7e.png align="center")
    
    ```sh
    kubectl get all (or)
    kubectl get svc
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486940575/a9598426-cb83-41d9-8026-55115cd54ea2.png align="center")
    
    Will verify if the YouTube app is accessible from Kubernetes.
    
* &lt;kubernetes-worker-ip:svc port&gt;
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722486964302/916c0925-2a20-47e9-9423-365843d9e17e.png align="center")
    
    Delete view:
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722487026235/8e8169cf-bd73-4885-bec5-114fedc19f8a.png align="center")
    
    ResourIt will apply the deployment. ### Stage View To verify if the YouTube app is accessible from Kubernetes, use: \`\`\`sh kubectl get all
    
    or
    
    ```sh
    kubectl get svc
    ```
    
    Access the app at:
    
    ```bash
    <kubernetes-worker-ip:svc port>
    ```
    
    ### Delete View
    
    Resources used in AWS
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1722487033940/a1c3bf3b-3312-4645-878f-487036a3cb7e.png align="center")