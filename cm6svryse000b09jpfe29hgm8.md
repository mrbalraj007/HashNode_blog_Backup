---
title: "Comprehensive CI/CD Pipeline for Private AKS: Seamless Integration with Azure DevOps, FrontDoor, Terraform, Private ACR, Azure SQL DB, Application Gat"
datePublished: Thu Feb 06 2025 05:12:09 GMT+0000 (Coordinated Universal Time)
cuid: cm6svryse000b09jpfe29hgm8
slug: comprehensive-cicd-pipeline-for-private-aks-seamless-integration-with-azure-devops-frontdoor-terraform-private-acr-azure-sql-db-application-gat
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1738817729781/270b797c-1430-48c5-b01a-8039fd6fd67f.png
tags: sql-server, virtual-machine, azure, sql, terraform, application-development, azure-devops, aks, azure-application-gateway, azure-devops-openshift-redhat-cloudnative-microservices-technology-jobs-freshers-careeropportunities-training-learning-kubernetes-cka-ckad-cks-discounts-jobs-cloud-aws-azure-devops-certification-training-learning-coupons, azurefrontdoor, aksazure-kubernetes-services, vnet, acr, azure-acr

---

![Image](https://github.com/user-attachments/assets/6ea24f7c-8b6e-49e2-be82-5daa837a7c5d align="left")

This document explains how to set up a CICD pipeline for deploying a web application on a private AKS cluster. It includes integration with Azure SQL Database, Private Azure Container Registry (ACR), and Application Gateway using Terraform. It also discusses using Azure Front Door for global load balancing and content delivery.

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

* \[x\] Need to Create a Personal Access Token (PAT)
    
* \[x\] [Terraform code](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/Azure_DevOps_All_Projects/12_Real-Time-DevOps-Project_CI-CD_Terraform_ACR_Storage_WebApp_SQL_Front-Door/Terraform_Code)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * Update `terraform.tfvars`
        
* \[x\] Application Repo:
    
    * \[x\] [Infra as Code](https://github.com/mrbalraj007/infra-as-code.git)
        
    * \[x\] [Application Repo](https://github.com/mrbalraj007/etickets.git)
        

## Tools and Technologies: Used

* **Terraform**: Used for infrastructure as code to create and manage Azure resources.
    
* **Azure DevOps**: For CICD pipeline and repository management.
    
* **Docker**: For containerizing the application.
    
* **Kubernetes**: For orchestrating the deployment of the application on AKS.
    
* **Azure Front Door**: For global load balancing and content delivery.
    
* **Azure Key Vault**: For storing secrets and sensitive information.
    
* **Azure CLI**: For managing Azure resources from the command line.
    
* **Azure Log Analytics Workspace**: For collecting and analyzing logs.
    
* **Azure Private Endpoint**: For secure access to Azure services within the VNet.
    
* **Azure Virtual Network (VNet)**: For isolating resources and enabling secure communication.
    
* **Azure Application Gateway**: For load balancing and routing traffic to the AKS cluster.
    
* **Azure Container Registry (ACR)**: For storing Docker images.
    
* **Azure SQL Database**: For storing application data.
    
* **Azure Kubernetes Service (AKS)**: For deploying the web application in a private cluster.
    

## Key Points

**Architecture Components**:

* **Virtual Networks**: Three virtual networks (AKS vnet, ACR vnet, Agent vnet) are used to isolate resources.
    
* **Private AKS Cluster**: Deployed within the AKS vnet.
    
* **Application Gateway**: Integrated with the AKS cluster for load balancing.
    
* **Private ACR**: Hosted within the ACR vnet.
    
* **Self-hosted Agent**: A virtual machine in the Agent vnet configured as a self-hosted agent for Azure DevOps.
    
* **Virtual Network Peering**: Enables communication between the virtual networks.
    
* **Private Endpoints**: Used for secure communication between services.
    
* **Private DNS Zones**: For name resolution within the virtual networks.
    
* **Azure SQL Database**: Backend database for the application.
    
* **Log Analytics Workspace**: For collecting and analyzing logs.
    
* **Ingress Controller**: Integrated with Application Gateway for routing traffic to the pods.
    

## Setting Up the Infrastructure

First, we'll create the necessary virtual machines using `terraform` code.

Below is a terraform Code:

> Once you [clone repo](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/Azure_DevOps_All_Projects/12_Real-Time-DevOps-Project_CI-CD_Terraform_ACR_Storage_WebApp_SQL_Front-Door/Terraform_Code) and run the terraform command.

```bash
$ ls -l
-rw-r--r-- 1 bsingh 1049089  690 Jan 31 15:01 DevOps_UI.tf
-rw-r--r-- 1 bsingh 1049089 6115 Jan 31 15:01 group_lib.tf
-rw-r--r-- 1 bsingh 1049089 3011 Jan 29 09:47 KeyVault_Create_permission.tf
-rw-r--r-- 1 bsingh 1049089  921 Jan 24 13:03 KeyVault-Getdata.tf
-rw-r--r-- 1 bsingh 1049089  675 Jan 30 11:41 output.tf
-rw-r--r-- 1 bsingh 1049089  813 Jan 24 13:03 provider.tf
-rw-r--r-- 1 bsingh 1049089  326 Jan 24 13:03 ssh_key.tf
-rw-r--r-- 1 bsingh 1049089  924 Jan 30 11:29 Storage.tf
-rw-r--r-- 1 bsingh 1049089 4248 Jan 31 20:36 terraform.tfvars
-rw-r--r-- 1 bsingh 1049089 3727 Jan 30 11:58 variable.tf
```

You need to run the following terraform command.

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply 
# Optional <terraform apply --auto-approve>
```

![Image](https://github.com/user-attachments/assets/8747070b-baf2-404a-9894-656eaa027df4 align="left")

Once you run the terraform command, then we will verify the following things to make sure everything is set up via a terraform.

### Inspect the `Cloud-Init` logs:

Once connected to VM then you can check the status of the `user_data` script by inspecting the log files

```bash
# Primary log file for cloud-init
sudo tail -f /var/log/cloud-init-output.log
                    or 
sudo cat /var/log/cloud-init-output.log | more
```

* *If the user\_data script runs successfully, you will see output logs and any errors encountered during execution.*
    
* *If there’s an error, this log will provide clues about what failed.*
    

## Detailed Steps

1. **Setting Up the Architecture**:
    
    * **Azure Repos**: Store the source code and Terraform configuration files.
        
    * **Azure Storage Account**: Store the Terraform state file securely.
        
    * **Build Pipeline**: Configure tasks to build and push Docker images.
        
    * **Release Pipeline**: Configure tasks to deploy resources using Terraform.
        
2. **Configuring the Build Pipeline**:
    
    * **Build and Push Docker Image**: Use Docker to build the application image and push it to ACR.
        
    * **Publish Terraform Files**: Publish Terraform configuration files as pipeline artifacts.
        
3. **Configuring the Release Pipeline**:
    
    * **Initialize Terraform**: Initialize the Terraform working directory and download necessary plugins.
        
    * **Apply Terraform Configuration**: Apply the Terraform configuration to create resources in Azure.
        
    * **Deploy Web App**: Deploy the Docker image to AKS.
        
    * **Configure Auto-Scaling and Alerts**: Set up auto-scaling rules and alert notifications.
        
4. **Terraform Configuration Files**:
    
    * **Provider Configuration**: Define the Azure provider and authentication details.
        
    * **Variable Definitions**: Define variables for resource names, locations, and other configurations.
        
    * **Main Configuration**: Define the resources to be created, including VNets, AKS cluster, ACR, SQL Database, Application Gateway, and Private Endpoints.
        
5. **Creating Service Connections**:
    
    * **Azure Service Connection**: Authenticate with Azure for deploying resources.
        
    * **Docker Registry Service Connection**: Authenticate with ACR for pushing Docker images.
        
6. **Pipeline Stages**:
    
    * **Infrastructure Creation**: Using Terraform to create virtual networks, virtual machines, ACR, SQL Database, Application Gateway, and AKS cluster.
        
    * **Application Deployment**: Building and pushing Docker images to ACR, deploying the application to AKS, and configuring Ingress and Application Gateway.
        
    * **Database Configuration**: Setting up the SQL database and running migrations.
        
    * **Monitoring and Logging**: Configuring Log Analytics Workspace for monitoring.
        
7. **Azure Front Door**:
    
    * **Global Load Balancing**: Distributes traffic across multiple backend endpoints.
        
    * **Content Delivery Network (CDN)**: Caches and delivers static and dynamic content closer to users.
        
    * **SSL/TLS Termination**: Offloads encryption and decryption processes.
        
    * **Application Layer Security**: Provides protection against common web application threats.
        
    * **Session Affinity**: Ensures subsequent requests from the same client are directed to the same backend server.
        
    * **Health Monitoring and Failover**: Continuously monitors backend endpoints and reroutes traffic if necessary.
        

### Step-by-Step Process:

#### Configure Service Connection

* First, create a [Service Connection](https://www.youtube.com/watch?v=pSmKNbN_Y4s) in Azure DevOps.
    
* Once you create a connection then note it down the connection Name, because that name will be used in the pipeline.
    
* On the agent machine, ensure you are logged in with Azure, and the connection is active. If not, log in using the following steps.
    
    ```sh
    az login --use-device-code
    ```
    
* Need to Create a service Connect in pipeline first.
    
    * Azure Service Connection
        
        ![Image](https://github.com/user-attachments/assets/e10917fd-fb50-4971-af44-092d36c49e3d align="left")
        
        ![Image](https://github.com/user-attachments/assets/e2199467-ae79-4ad4-a679-cd7a5eb22610 align="left")
        
        ![Image](https://github.com/user-attachments/assets/8b5f1b92-0891-43c8-9a58-5ca27427952c align="left")
        
        ![Image](https://github.com/user-attachments/assets/1e7020fc-8a7d-4c68-bb7a-9b101ce8dc55 align="left")
        
        ![Image](https://github.com/user-attachments/assets/ea313399-e8b3-402d-8cb1-875f6e698b27 align="left")
        
        ![Image](https://github.com/user-attachments/assets/1fe802f7-bb27-4df1-af33-9b1ca6d19188 align="left")
        

#### Update Secret variable value details.

* Update secret variable value first
    
    * update `servicePrincipalId`
        
    * update `servicePrincipalKey`
        
    * update `tenantid`
        
* **Step-01**. Go to key vault and update the value (Azure UI)
    
    * Update the other two values in the same way.
        
* **Step-02**. Update the secret in Library at Azure DevOps  
    
    ![Image](https://github.com/user-attachments/assets/f06b8daa-65d8-4999-90a6-e222f61336ad align="left")
    
* **Step-03**. Link secrets from an Azure key vault as variables..
    
    * ![Image](https://github.com/user-attachments/assets/3f1ee30c-c31e-47e4-b554-dd0367c185f2 align="left")
        
        ![Image](https://github.com/user-attachments/assets/7a8e2a60-2862-460e-aefd-e313a588a0b9 align="left")
        
        ![Image](https://github.com/user-attachments/assets/cda11888-d173-4c83-8bfa-440f00d5aaa2 align="left")
        
        ![Image](https://github.com/user-attachments/assets/00bf3b2c-4edc-4a1c-a7cd-c21b9232a5d1 align="left")
        
        ![Image](https://github.com/user-attachments/assets/8edb341d-47a4-4777-9af0-f9011e3a94b0 align="left")
        
        ![Image](https://github.com/user-attachments/assets/976fc27e-27b1-4118-bcc7-fa39cbc2e245 align="left")
        

#### Update changes in the repo code as per project details.

* Repo (Infra-as-code)
    
    * Step-01: `script file` need to be updated from `agent-vm` folder
        
        * update the `Organization` and `PAT token`
            
            ![Image](https://github.com/user-attachments/assets/08b262af-bf4f-4a54-b9ee-ffe5e23b4778 align="left")
            
    * Step-02: Update Service Principle Name from `private-acr` Folder
        
        * To get the Service Principal name
            
            * List Service Principals:
                
        
        ```sh
        az ad sp list --query <query_string>
        
        Example:
        az ad sp list --query "[].{Name:displayName, AppId:appId}" --output table
        
        # filder with name for your service account
        az ad sp list --query "[?starts_with(displayName, 'Azure')].{Name:displayName, AppId:appId}" --output table
        ```
        
        * Update the Service Principal name:
            
            ![Image](https://github.com/user-attachments/assets/7b9db8ed-2991-44e0-a79b-e80db59921ae align="left")
            

### Build a YAML pipeline.

* Following is the sequence to create the setup using pthe ipeline.
    
    * VNet (Network/Subnet)
        
    * Agent VM
        
    * Azure Container Registry
        
    * SQL Database
        
    * Application Gateway
        
    * AKS Cluster
        
    * FrontDoor
        
* Step-01: Build the YAML pipeline as below:  
    
    ![Image](https://github.com/user-attachments/assets/ab364f80-8ca9-40c6-b049-66e96353d0c9 align="left")
    
    ![Image](https://github.com/user-attachments/assets/d8256fb6-02da-4e09-aa16-aca69699dd1e align="left")
    
    ![Image](https://github.com/user-attachments/assets/264e7e8f-8074-46c1-84e9-69dfc3c684d5 align="left")
    
    ![Image](https://github.com/user-attachments/assets/32bceb0a-b008-49b0-b201-98edbd8cd7e4 align="left")
    
* Adjust the parameters for `vNet` Job.  
    
    ![Image](https://github.com/user-attachments/assets/fb6b8e3e-fb2a-405a-bf5a-7fbc2d9a1841 align="left")
    
* Adjust the parameters for `vm` Job.  
    
    ![Image](https://github.com/user-attachments/assets/28d63092-c966-4bb5-8869-8de0cefff925 align="left")
    
* Adjust the parameters for `acr` Job.  
    
    ![Image](https://github.com/user-attachments/assets/e0e495b4-a3f6-4b53-abc9-8f43a5318829 align="left")
    
* Save and run the pipeline.  
    
    ![Image](https://github.com/user-attachments/assets/4fe7c114-058a-46f4-8a12-7e3faa2c8bee align="left")
    
* Adjust the parameters for `db` Job.  
    
    ![Image](https://github.com/user-attachments/assets/a2fe3359-b438-40ec-9a33-434e75eef20e align="left")
    
* Adjust the parameters for `appgateway` Job.  
    
    ![Image](https://github.com/user-attachments/assets/abd46cdc-85e8-4d74-9cab-b9541efcf840 align="left")
    
* Adjust the parameters for `AKS Cluster` Job.  
    
    ![Image](https://github.com/user-attachments/assets/3b5de562-b9ed-48f2-b447-0a93779a64e3 align="left")
    
* Adjust the parameters for `FrontDoor` Job.  
    
    ![Image](https://github.com/user-attachments/assets/27553262-b0cd-4bd6-a380-29922d336e07 align="left")
    
* Step-02: Adjust the `ssh key` in library for `AKS cluster`
    
    * `SSH public key` need to be updated
        
        ![Image](https://github.com/user-attachments/assets/046a1093-a9ea-4908-9b9b-85895ae46a2a align="left")
        

#### Run the pipeline.

![Image](https://github.com/user-attachments/assets/432c3290-558c-47b1-a01f-0a9a115896a3 align="left")

* Here is the 👉[Updated pipeline for Create Infra](https://github.com/mrbalraj007/Azure_DevOps_Projects/blob/main/Azure_DevOps_All_Projects/12_Real-Time-DevOps-Project_CI-CD_Terraform_ACR_Storage_WebApp_SQL_Front-Door/Pipeline/Create%20Infra.md)👈
    

### Install SQL package on Agent VM.

* Open a PuTTY session for the virtual machine and install the required SQL package to check SQL connectivity.
    
    * Install mssql-tools:
        
        * sudo apt-get update
            
        * sudo apt-get install mssql-tools -y
            
    
    * 3\. Locate the sqlcmd executable:
        
        * ls /opt/mssql-tools/bin/sqlcmd
            
    * Add the sqlcmd directory to your PATH:
        
        * echo 'export PATH="$PATH:/opt/mssql-tools/bin"' &gt;&gt; ~/.bashrc
            
    * Apply the changes:
        
        * source ~/.bashrc
            
    * Verify if the PATH is updated:
        
        * Run the following command to check if the path has been correctly added:
            
            * echo $PATH
                
    
    ![Image](https://github.com/user-attachments/assets/b51291e1-9bbc-4b70-9c1c-2e90b5bcddba align="left")
    
    * Verify the installation:
        
        * sqlcmd -S &lt;server\_address&gt; -U -P -d &lt;database\_name&gt;
            
            ![Image](https://github.com/user-attachments/assets/cdfb5be6-bd1b-4861-b36b-beeb50e12b5e align="left")
            
* Convert SQL cred to base 64
    

```bash
echo "Server=tcp:yourserver.database.windows.net,1433;Initial Catalog=yourdatabase;Persist Security Info=False;User ID=yourusername;Password=yourpassword;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" | base64 | tr -d '\n'
```

![Image](https://github.com/user-attachments/assets/17975644-1a11-410a-9c09-a137f9d10661 align="left")

* Decode base64 cred to Normal.
    

```sh
 echo "U2VydmVyPXRjcDpldGlja2V0ZGJzZXJ2ZXIzMDAxMjAyNS5kYXRhYmFzZS53aW5kb3dzLm5ldCwxNDMzO0luaXRpYWwgQ2F0YWxvZz1ldGlja2V0cy1kYjtQZXJzaXN0IFNlY3VyaXR5IEluZm89RmFsc2U7VXNlciBJRD1henVyZXVzZXI7UGFzc3dvcmQ9cGFzc3dvcmRAMTIzO011bHRpcGxlQWN0aXZlUmVzdWx0U2V0cz1GYWxzZTtFbmNyeXB0PVRydWU7VHJ1c3RTZXJ2ZXJDZXJ0aWZpY2F0ZT1G" | base64 -d
```

![Image](https://github.com/user-attachments/assets/b6f901f4-e941-415b-b264-6248f87ffbda align="left")

### Connect `AKS cluster` on Agent VM.

* Run the following commands to connect AKS cluster
    
    * Login to your azure account
        
    
    ```sh
    az login
    ```
    
    * Set the cluster subscription
        
    
    ```sh
    az account set --subscription <ID>
    ```
    
    * Download cluster credentials
        
    
    ```sh
    az aks get-credentials --resource-group rg-devops --name aksdemo --overwrite-existing
    ```
    

![Image](https://github.com/user-attachments/assets/5c586034-bd57-49df-8bd3-6a481133bba8 align="left")

### Verify Application Accessibility.

* Try to access the application gateway IP in the browser, and you will see the error message below.
    
    ![Image](https://github.com/user-attachments/assets/5f26931b-b116-4747-804c-da85aa32ab80 align="left")
    
    ![Image](https://github.com/user-attachments/assets/33ae7b59-c0b6-432d-86f0-80b1fceabb9c align="left")
    

## Build the second pipeline (Application).

* Build application pipeline
    
* Here is the updated file.
    
* To get the all repositories in Azure Container Registry
    
    * You can use the Azure CLI to check the details of repositories and images stored in a private Azure Container Registry (ACR).
        
        1. List all repositories in Azure Container Registry
            
        
        ```sh
        az acr repository list --name <ACR_NAME> --output table
        🔹 Replace <ACR_NAME> with the name of your Azure Container Registry.
        ```
        
        2. Get details of a specific repository
            
        
        ```sh
        az acr repository show --name <ACR_NAME> --repository <REPOSITORY_NAME>
        🔹 Replace <REPOSITORY_NAME> with the name of the repository inside ACR.
        ```
        
        3. List all image tags in a repository
            
        
        ```sh
        az acr repository show-tags --name <ACR_NAME> --repository <REPOSITORY_NAME> --output table
        🔹 This command displays the available tags for a given repository.
        ```
        
        4. List all manifests of an image repository
            
        
        ```sh
        az acr repository show-manifests --name <ACR_NAME> --repository <REPOSITORY_NAME> --output table
        🔹 This command provides manifest details of all images in the repository.
        ```
        
        5. Show details of a specific image tag
            
        
        ```sh
        az acr repository show --name <ACR_NAME> --repository <REPOSITORY_NAME> --image <IMAGE_TAG>
        🔹 Replace <IMAGE_TAG> with the tag of the image you want details for.
        ```
        
        6. Check the last update timestamp of an image
            
        
        ```sh
        az acr repository metadata list --name <ACR_NAME> --repository <REPOSITORY_NAME>
        🔹 This provides metadata information, including the last update timestamp.
        ```
        
    * Update the deployment.yaml file as below for `SQL Connection` and image name in `manifest` file.
        
    * Run the pipeline.
        
        * It will ask for permission and approve it.
            
        * AKS deployment is waiting for approval
            

### Troubleshooting.

* I was getting an error `imagepullbackoff` and noticed that the `SQL connection` name was having a problem. To retrieve and update the string from Secret.
    
* Retrieve and Update the Connection String from the Secret
    
    * Since we're using a Kubernetes secret for the database connection, check the secret value:
        
    
    ```bash
    kubectl get secret db-connection-secret -o jsonpath="{.data.connection-string}" | base64 --decode
    ```
    
* Pipeline status
    
* ![Image](https://github.com/user-attachments/assets/8111a79c-376f-4551-9674-a6b5c553ffdb align="left")
    
* Image status  
    
    ![Image](https://github.com/user-attachments/assets/d684659b-7fb5-4fd0-a03c-f9a766a5c730 align="left")
    
* Application is accessible now from Application Gateway.  
    
    ![Image](https://github.com/user-attachments/assets/1e298a29-52e7-48b7-972b-e355b90de3ad align="left")
    
* Try to create account in website.  
    
    ![Image](https://github.com/user-attachments/assets/724fa414-c3d2-4d50-82dc-5ed9676eb054 align="left")
    
* Following are the cred to login into webpage  
    
    ![Image](https://github.com/user-attachments/assets/28a139cc-9e6d-4eec-ac18-0e5214bec229 align="left")
    

## Application accessibility from Front Door.

![Image](https://github.com/user-attachments/assets/96667f9e-611c-4286-8576-6f386a815670 align="left")

![Image](https://github.com/user-attachments/assets/40bee6c7-2f28-43f1-a214-022d43fba9ea align="left")

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text"><strong>Congratulations</strong> :-) the application is working and accessible.</div>
</div>

![Image](https://github.com/user-attachments/assets/5a9efd1f-1584-4b6a-bb13-4756c44ea252 align="left")

* **Pipeline Status**
    
    ![Image](https://github.com/user-attachments/assets/4e1fb7d3-6aad-489e-9f82-ab015e539b0f align="left")
    

## Pipeline for Cleanup Infra Setup.

* Following is the sequence to delete the setup using the pipeline.
    
    * FrontDoor
        
    * AKS Cluster
        
    * Application Gateway
        
    * Azure Container Registry
        
    * SQL Database
        
    * Agent VM
        
    * VNet
        
* Here is the 👉[Updated pipeline for delete](https://github.com/mrbalraj007/Azure_DevOps_Projects/blob/main/Azure_DevOps_All_Projects/12_Real-Time-DevOps-Project_CI-CD_Terraform_ACR_Storage_WebApp_SQL_Front-Door/Pipeline/Destroy%20Infra.md)👈
    

![Image](https://github.com/user-attachments/assets/701d8225-1977-4b98-9a8c-500c5b26b956 align="left")

## Environment Cleanup:

* As we are using Terraform, we will use the following command to delete `ssh_key`, `Vault` and `Storage account`.
    
* Run the terraform command.
    
    ```bash
    Terraform destroy --auto-approve
    ```
    
* I was getting the below error message while deleting the setup. It is getting failed in deleting `ResourceGroup`
    
    * Rerun the destroy command and it will delete `ResourceGroup` as well.
        
        ![Image](https://github.com/user-attachments/assets/86309505-ce7b-47d1-9459-4e4e9b3f194d align="left")
        

## Conclusion

This document outlines the comprehensive setup of a secure and efficient CICD pipeline for deploying a web application on a private AKS cluster, integrated with various Azure services. By leveraging Terraform for infrastructure as code, Azure DevOps for pipeline management, and Azure Front Door for global load balancing, the solution ensures high availability, security, and optimal performance for the application.

For detailed steps and configurations, refer to the provided Terraform scripts and Azure DevOps pipeline definitions.

**Ref Link:**

* [Azure FrontDoor- YouTube Link](https://www.youtube.com/watch?v=KplNosFAxXU)
    
    * [Rest of Infra Setup- YouTube Link](https://www.youtube.com/watch?v=PDBoprlJfUw)
        
* [Standard and Basic SKU](https://learn.microsoft.com/en-us/answers/questions/1349124/azure-cannot-change-the-static-ip-address)
    
* [Terraform code for azurerm\_public\_ip](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/public_ip)
    
* [Terraform code for azurerm\_kubernetes\_cluster](https://registry.terraform.io/providers/hashicorp/azurerm/2.17.0/docs/resources/kubernetes_cluster)
    
* [AKS cluster\_node\_pool](https://registry.terraform.io/providers/hashicorp/azurerm/2.48.0/docs/resources/kubernetes_cluster_node_pool)
    
* [Kubernetes versions details](https://learn.microsoft.com/en-us/azure/aks/supported-kubernetes-versions?tabs=azure-cli#supported-version-list)
    
* [Billing FAQs](https://learn.microsoft.com/en-us/azure/devops/organizations/billing/billing-faq?view=azure-devops&viewFallbackFrom=vsts&tabs=new-nav)