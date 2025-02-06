---
title: "Spotify on Terraform: A DevOps Journey!"
datePublished: Fri Oct 25 2024 01:16:31 GMT+0000 (Coordinated Universal Time)
cuid: cm2o1kciz000008lkg6pygeur
slug: spotify-on-terraform-a-devops-journey
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1729818488850/8754d674-91d2-4538-be9a-d2aff69d1a19.gif
tags: code, apps, docker, github, devops, applications, containers, spotify, terraform, containerization, spotify-api

---

In this blog, we will use Terraform to construct many Spotify playlists. Terraform will automatically create and maintain these playlists.

![Spotify](https://github.com/user-attachments/assets/aa0dd720-76e3-444d-939c-c48fdfb4cd91 align="left")

## Prerequisites

Before getting started on this project, you need to be familiar with the following tools and accounts:

* \[x\] [Clone repository for terraform code](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/16.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * from spotify/.env (i.e store your Spotify application's Client ID and Secret:  
        
        ```bash
        SPOTIFY_CLIENT_ID=<your_spotify_client_id>
        SPOTIFY_CLIENT_SECRET=<your_spotify_client_secret>)
        ```
        
    * from `Code_IAC_Terraform_box`/[main.tf](http://main.tf) (i.e key name- `MYLABKEY`\*)
        

* \[x\] **Terraform**: A popular Infrastructure as Code (IaC) tool used to automate cloud resources, installed on your local machine. You can download it from the official Terraform website, and ensure it's correctly installed by running the Terraform version command.
    
* \[x\] **Docker**: Required to run a container that helps with Spotify account authentication.
    
* \[x\] **Spotify Account**: A standard Spotify account, either free or premium, is needed to create and manage playlists.
    
* \[x\] [**Spotify Developer Account**](https://developer.spotify.com/): This allows access to API credentials (Client ID, Client Secret, and API Key), which are essential for interacting with Spotify through Terraform.
    
* \[x\] **VS Code**: A code editor used to write Terraform configuration files.
    

## Key Benefits of Using Terraform with Spotify:

* **Automation**: Automates the process of creating Spotify playlists. Instead of manually creating playlists and adding tracks, you can run Terraform scripts to manage playlists easily.
    
* **Reusability**: Once the Terraform configuration is set, it can be reused to create multiple playlists across various Spotify accounts.
    
* **Hands-on Learning**: This project introduces several basic Terraform concepts, such as providers, modules, data blocks, and API integration, making it perfect for beginners learning Infrastructure as Code.
    

### **Task 01**. Register a [**Spotify Developer Account**](https://developer.spotify.com/), then log in and create an application.

Once you log into your Spotify account then click on Create app

![image](https://github.com/user-attachments/assets/4c9efee0-b658-41d8-8e5f-337951850549 align="left")

Fill in the name and description from the table below, check the box to agree to the terms of service, and then click Create.

| Name | Description |
| --- | --- |
| Terraform Playlist- BS | Create a Spotify playlist using Terraform. |

Copy and paste the URI below into the Redirect URI field, then click Add so that Spotify can discover its authorization application on port `27228` at the right location. Scroll to the bottom of the form and choose Save.

```sh
http://localhost:27228/spotify_callback
```

![image-1](https://github.com/user-attachments/assets/cbfdd324-0f4d-47b5-b3d8-0ee25f1c4ebd align="left")

* To connect with Spotify's API, you need a Client ID and Client Secret.
    

Once Spotify has created the application, find and click the green `Edit Settings` icon in the upper right corner. Make sure to write down the Client ID and Client Secret..

![image-2](https://github.com/user-attachments/assets/ac922454-f03d-4f58-b619-f387cd50ca26 align="left")

![image-3](https://github.com/user-attachments/assets/29405788-fb88-4c18-956f-c305f93b4a96 align="left")

![image-4](https://github.com/user-attachments/assets/15e438c7-b0eb-4c95-bc7c-b49e281ea884 align="left")

## Setting Up the Environment

I have created Terraform code to set up the entire environment, including installing the necessary applications, tools, and an authorization proxy server. This server allows Terraform to interact with Spotify.

* \[x\] [Clone repository for terraform code](https://github.com/mrbalraj007/DevOps_free_Bootcamp/tree/main/16.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * from `spotify/.env` (i.e store your Spotify application's `Client ID` and `Secret`:  
        
        ```bash
        SPOTIFY_CLIENT_ID=<your_spotify_client_id>
        SPOTIFY_CLIENT_SECRET=<your_spotify_client_secret>)
        ```
        
    * from Spotify/[playlist.tf](http://playlist.tf) (change the `artist name`)
        
    * from `Code_IAC_Terraform_box`/[main.tf](http://main.tf) (i.e key name- `MYLABKEY`\*)
        
* ⇒ EC2 machines will be created and named as `"Terraform-svr".`
    
* ⇒ Docker Install
    

### EC2 Instance creation

* To Create a Virtual machine `Terraform-svr`.
    

Below is a terraform configuration:

Once you [clone the repo](https://github.com/mrbalraj007/DevOps_free_Bootcamp.git) then go to folder *"16.Real-Time-DevOps-Project/Terraform\_Code/Code\_IAC\_Terraform\_box"* and run the terraform command.

```bash
cd Terraform_Code/Code_IAC_Terraform_box

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
dar--l          23/10/24  11:49 AM                spotify
-a---l          29/09/24  10:44 AM            507 .gitignore
-a---l          21/10/24  10:47 PM           1866 main.tf
-a---l          16/07/21   4:53 PM           1696 MYLABKEY.pem
```

```bash
16.Real-Time-DevOps-Project/Terraform_Code/Code_IAC_Terraform_box/

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
dar--l          23/10/24  11:49 AM                spotify
-a---l          29/09/24  10:44 AM            507 .gitignore
-a---l          21/10/24  10:47 PM           1866 main.tf
-a---l          16/07/21   4:53 PM           1696 MYLABKEY.pem
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

Once you run the Terraform command, we will check the following things to ensure everything is set up correctly using Terraform.

### Inspect the `Cloud-Init` logs:

Once connected to EC2 instance then you can check the status of the `user_data` script by inspecting the \[log files\]

```bash
# Primary log file for cloud-init
sudo tail -f /var/log/cloud-init-output.log
                    or 
sudo cat /var/log/cloud-init-output.log | more
```

* If the user\_data script runs successfully, you will see output logs and any errors encountered during execution.
    
* If there’s an error, this log will provide clues about what failed.
    
* Outcome of "`cloud-init-output.log`"
    

![image-11](https://github.com/user-attachments/assets/ab0137bf-87c0-4356-ae50-81e8153d339a align="left")

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

When you review `cloud-init-output.log` then you will get the following outcomes.

```bash
0a5fcebc27b3   ghcr.io/conradludgate/spotify-auth-proxy   "/bin/spotify_auth_p…"   2 minutes ago   Up 2 minutes   0.0.0.0:27228->27228/tcp, :::27228->27228/tcp   zen_engelbart
Container logs for debugging:
APIKey:   G71jLPKnPQG_yaGmbLPfNYYtbeNnDW_0YuVzRSVAuWlAfw5DfUbNSohsTk3KJeRn
Auth URL: http://localhost:27228/authorize?token=uT4xo8nLCgdkX6M4tbyWikvbbf6CKj9c9-G-68Y37fkyzOI0DCKhxW8RfWh2oXqC
Debug: APIKey retrieved is 'G71jLPKnPQG_yaGmbLPfNYYtbeNnDW_0YuVzRSVAuWlAfw5DfUbNSohsTk3KJeRn'
APIKey successfully retrieved: G71jLPKnPQG_yaGmbLPfNYYtbeNnDW_0YuVzRSVAuWlAfw5DfUbNSohsTk3KJeRn
Updating terraform.tfvars with the retrieved APIKey...
To show container logs...
APIKey:   G71jLPKnPQG_yaGmbLPfNYYtbeNnDW_0YuVzRSVAuWlAfw5DfUbNSohsTk3KJeRn
Auth URL: http://localhost:27228/authorize?token=uT4xo8nLCgdkX6M4tbyWikvbbf6CKj9c9-G-68Y37fkyzOI0DCKhxW8RfWh2oXqC
ubuntu@Terraform-svr:~$
```

You have to copy the `Auth URL` and open it in the browser

```bash
<http://<publicIPaddress of EC2 instance>:27228/authorize?token=uT4xo8nLCgdkX6M4tbyWikvbbf6CKj9c9-G-68Y37fkyzOI0DCKhxW8RfWh2oXqC>
# You should use your own token
```

```bash
http://54.210.254.122:27228/authorize?token=uT4xo8nLCgdkX6M4tbyWikvbbf6CKj9c9-G-68Y37fkyzOI0DCKhxW8RfWh2oXqC
```

The first time you run it, it will display like this, but you need to run it again until you are successfully authenticated.

![image-13](https://github.com/user-attachments/assets/4ee6f200-948f-4036-b1ac-e5ca76f2e684 align="left")

Now, again you have to type below in broser.

![image-12](https://github.com/user-attachments/assets/47ccac0a-0967-4b3a-a363-66155762f8ab align="left")

**Note**⇒ To get Spotify provider info

```sh
https://registry.terraform.io/providers/conradludgate/spotify/latest/docs
https://github.com/conradludgate/terraform-provider-spotify?tab=readme-ov-file
```

### Create Terraform playlist

Now, go to the folder `spotify` and initiate a terraform command

```bash
cd spotify
ubuntu@Terraform-svr:~/spotify$ ls
MYLABKEY.pem  provider.tf  terraform.tfvars  variable.tf
ubuntu@Terraform-svr:~/spotify$
```

Now, run the following command.

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply 
# Optional <terraform apply --auto-approve>
```

### Verify Spotify playlist

Go to [https://open.spotify.com/](https://open.spotify.com/)

You will see the playlist below.

![image-8](https://github.com/user-attachments/assets/7d1ce1b5-ab75-41ab-9d4d-000edec6b4a8 align="left")

![image-9](https://github.com/user-attachments/assets/7b7832a9-1116-4732-bd2b-c3ddf77cc174 align="left")

![image-10](https://github.com/user-attachments/assets/0874a356-c52d-4fcc-9d6b-0ca750757de4 align="left")

Congratulations! :-) You have successfully deployed the playlist using Terraform.

### Resources used in AWS:

* EC2 instances
    

## Environment Cleanup:

* As we are using Terraform, we will use the following command to delete
    
    * `Playlist` first
        
    * then delete the `virtual machine`.
        

#### To delete `Spotify playlist`

* Login into the Terraform EC2 instance, change the directory to `Spotify` and run the following command to delete the playlist.
    

```bash
cd /spotify
sudo terraform destroy --auto-approve
```

#### Now, time to delete the `Virtual machine`.

Go to folder *"16.Real-Time-DevOps-Project/Terraform\_Code/Code\_IAC\_Terraform\_box"* and run the terraform command.

```bash
cd Terraform_Code/Code_IAC_Terraform_box

$ ls -l
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
da---l          26/09/24   9:48 AM                Code_IAC_Terraform_box

Terraform destroy --auto-approve
```

## Conclusion

By using Terraform to manage your Spotify playlists, you automate a previously tedious process and gain valuable hands-on experience with Infrastructure as Code. This project explains key Terraform principles and shows how IaC can be applied beyond cloud infrastructure to everyday products like Spotify. Whether you're new to Terraform or looking to enhance your automation skills, this project offers an enjoyable and practical way to improve your understanding of API connections and resource management. With reusable setups and automation, managing playlists has never been easier!

Blog Reference: For a detailed breakdown of the technical aspects, you can refer to the complete technical blog post that the user helped write. This blog covers the entire process step-by-step, ensuring a smooth implementation of the project.

**Ref Link**

* [YouTube Link](https://www.youtube.com/watch?v=LjJLZRi_zGU&list=PLJcpyd04zn7p_nI0hoYRcqSqVS_9_eLaR&index=101)
    
* [Create a Spotify playlist with Terraform](https://developer.hashicorp.com/terraform/tutorials/community-providers/spotify-playlist)
    
* [Install Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli)