---
title: "CI/CD Pipeline for Private AKS: Integrating Azure DevOps, Terraform, Private ACR, Azure SQL DB, Application Gateway, Azure Key Vault, and Storage"
datePublished: Mon Feb 03 2025 03:45:25 GMT+0000 (Coordinated Universal Time)
cuid: cm6oicuwq000m0al49u635u4h
slug: cicd-pipeline-for-private-aks-integrating-azure-devops-terraform-private-acr-azure-sql-db-application-gateway-azure-key-vault-and-storage
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1738553627212/ca6b6c6c-1843-432a-97c6-9bf0e142aeb3.png
tags: sql-server, gateway, docker, sql, devops, terraform, vault, cicd-cjy1vtdk2005kjjs17n8couc3, docker-image, azure-key-vault, aksazure-kubernetes-services, storageaccount, acr, applicate-gateway, strorage

---

![Image](https://github.com/user-attachments/assets/00ba4653-adaa-4422-b3b1-fb94654c4f2a align="left")

This document explains how to set up a CI/CD pipeline using Azure DevOps, Terraform, and different Azure services to deploy a web application on a private AKS cluster. The deployment is automated and ensures secure communication between services.

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

Need to create a PAT Access Token-

* \[x\] [Clone repository for terraform code](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/Azure_DevOps_All_Projects/11_Real-Time-DevOps-Project_CI-CD_Terraform_ACR_Storage_WebApp_SQL)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * Update `terraform.tfvars`
        
* \[x\] [Infra as Code](https://github.com/mrbalraj007/infra-as-code.git)
    
* \[x\] [Application Repo](https://github.com/mrbalraj007/etickets.git)
    

1. **Azure Subscription**: An active Azure subscription is required to create and manage resources.
    
2. **Azure DevOps Account**: An Azure DevOps account to create and manage pipelines.
    
3. **Terraform**: Installed on the local machine or configured in the Azure DevOps pipeline.
    
4. **Azure CLI**: Installed on the local machine or configured in the Azure DevOps pipeline.
    
5. **Service Principal**: Created in Azure AD for authentication and authorization in the pipeline.
    

## Technologies/Services Used

1. **Azure Kubernetes Service (AKS)**: For deploying the web application in a private cluster.
    
2. **Azure SQL Database**: For storing application data.
    
3. **Azure Container Registry (ACR)**: For storing Docker images.
    
4. **Azure Application Gateway**: For load balancing and routing traffic to the AKS cluster.
    
5. **Azure Virtual Network (VNet)**: For isolating resources and enabling secure communication.
    
6. **Azure Private Endpoint**: For secure access to Azure services within the VNet.
    
7. **Azure Key Vault**: For storing secrets and sensitive information.
    
8. **Azure Log Analytics Workspace**: For collecting and analyzing logs.
    
9. **Terraform**: For infrastructure as code to automate resource creation.
    
10. **Azure DevOps**: For creating CI/CD pipelines.
    

## Key Points

1. **Architecture Overview**:
    
    * The architecture includes multiple VNets for AKS, ACR, and self-hosted agents.
        
    * Virtual Network Peering is used to enable communication between VNets.
        
    * Private Endpoints are used for secure access to Azure services.
        
2. **Build Pipeline**:
    
    * The build pipeline includes tasks to build and push a Docker image to ACR.
        
    * Terraform configuration files are published as pipeline artifacts for use in the release pipeline.
        
3. **Release Pipeline**:
    
    * The release pipeline is linked to the artifacts published by the build pipeline.
        
    * It includes stages for deploying resources to AKS using Terraform.
        
    * Auto-scaling and alert configurations are set up for the web app.
        
4. **Terraform Configuration**:
    
    * Separate files for providers, variables, and main configuration.
        
    * Variables are stored securely and referenced in the configuration files.
        
    * The configuration includes creating VNets, AKS cluster, ACR, SQL Database, Application Gateway, and Private Endpoints.
        
5. **Service Connections**:
    
    * Azure DevOps service connections are created for authentication with Azure and Docker Registry.
        

## Setting Up the Infrastructure

First, we'll create the necessary virtual machines using `terraform` code.

Below is a terraform Code:

Once you [clone repo](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/Azure_DevOps_All_Projects/11_Real-Time-DevOps-Project_CI-CD_Terraform_ACR_Storage_WebApp_SQL) and run the terraform command.

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

Now, run the following command.

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply 
# Optional <terraform apply --auto-approve>
```

![Image](https://github.com/user-attachments/assets/8747070b-baf2-404a-9894-656eaa027df4 align="left")

Once you run the terraform command, then we will verify the following things to make sure everything is setup via a terraform.

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
    
    * **Initialize Terraform**: Initialize the Terraform working directory and download the necessary plugins.
        
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
        

### Step-by-Step Process:

#### Configure Service Connection

* First, create [Service Connection](https://www.youtube.com/watch?v=pSmKNbN_Y4s) in Azure Devops.
    
* Once you create a connection, make sure to note down the connection name, as it will be used in the pipeline.
    
* On the agent machine, ensure you are logged in with Azure and that the connection is active. If not, log in using the following steps.
    
    ```sh
    az login --use-device-code
    ```
    
* Need to Create a service Connect in the pipeline first.
    
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
        
    * **Step-01**. Go to the key vault and update the value (Azure UI)
        
* Update the other two values in the same way.
    
    * **Step-02**. Update the secret in the Library at Azure DevOps
        
    * **Step-03**. Link secrets from an Azure key vault as variables..
        

#### Update changes in Repo code as per project details.

* Repo (Infra-as-code)
    
    * Step-01: `script file` need to be updated from `agent-vm` folder
        
        * update the `Organization` and `PAT token`
            
            ![Image](https://github.com/user-attachments/assets/08b262af-bf4f-4a54-b9ee-ffe5e23b4778 align="left")
            
    * Step-02: Update Service Principle Name from `private-acr` Folder
        
        * To get the Service Principal's name
            
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

* Following is the sequence to create the setup using the pipeline.
    
    * VNet (Network/Subnet)
        
    * Agent VM
        
    * Azure Container Registry
        
    * SQL Database
        
    * Application Gateway
        
    * AKS Cluster
        
* Step-01: Build YAML pipeline as below:
    
* ![Image](https://github.com/user-attachments/assets/ab364f80-8ca9-40c6-b049-66e96353d0c9 align="left")
    
    ![Image](https://github.com/user-attachments/assets/d8256fb6-02da-4e09-aa16-aca69699dd1e align="left")
    
    ![Image](https://github.com/user-attachments/assets/264e7e8f-8074-46c1-84e9-69dfc3c684d5 align="left")
    
    ![Image](https://github.com/user-attachments/assets/32bceb0a-b008-49b0-b201-98edbd8cd7e4 align="left")
    
    Adjust the parameters for `vNet` Job.
    
* ![Image](https://github.com/user-attachments/assets/fb6b8e3e-fb2a-405a-bf5a-7fbc2d9a1841 align="left")
    
* Adjust the parameters for `vm` Job.
    
* ![Image](https://github.com/user-attachments/assets/28d63092-c966-4bb5-8869-8de0cefff925 align="left")
    
* Adjust the parameters for `acr` Job.
    
* ![Image](https://github.com/user-attachments/assets/e0e495b4-a3f6-4b53-abc9-8f43a5318829 align="left")
    
* Save and run the pipeline.
    
* ![Image](https://github.com/user-attachments/assets/4fe7c114-058a-46f4-8a12-7e3faa2c8bee align="left")
    
* Adjust the parameters for `db` Job.
    
* ![Image](https://github.com/user-attachments/assets/a2fe3359-b438-40ec-9a33-434e75eef20e align="left")
    
* Adjust the parameters for `appgateway` Job.
    
* ![Image](https://github.com/user-attachments/assets/abd46cdc-85e8-4d74-9cab-b9541efcf840 align="left")
    
* Adjust the `ssh key` in library for `AKS cluster`
    
    * `SSH public key` need to be updated
        
        ![Image](https://github.com/user-attachments/assets/046a1093-a9ea-4908-9b9b-85895ae46a2a align="left")
        
    
    Run the pipeline.
    
* ![Image](https://github.com/user-attachments/assets/77964f28-2286-4764-b392-3d55afa9aa25 align="left")
    

### Install SQL package on Agent VM.

* Open a PuTTY session for the virtual machine and install the required SQL package to check SQL connectivity.
    
    * install mssql-tools
        
        * sudo apt-get update
            
        * sudo apt-get install mssql-tools -y
            
    * Find the sqlcmd executable:
        
        * ls /opt/mssql-tools/bin/sqlcmd
            
    * Add the sqlcmd directory to your PATH:
        
        * nano ~/.bashrc
            
        * export PATH="$PATH:/opt/mssql-tools/bin"
            
    * Apply the changes:
        
        * source ~/.bashrc
            
    
    ![Image](https://github.com/user-attachments/assets/b51291e1-9bbc-4b70-9c1c-2e90b5bcddba align="left")
    
    * Verify the installation:
        
        * sqlcmd -S &lt;server\_address&gt; -U -P -d &lt;database\_name&gt;
            
            ![Image](https://github.com/user-attachments/assets/cdfb5be6-bd1b-4861-b36b-beeb50e12b5e align="left")
            
* Convert SQL cred to base 64
    

```bash
echo "Server=tcp:yourserver.database.windows.net,1433;Initial Catalog=yourdatabase;Persist Security Info=False;User ID=yourusername;Password=yourpassword;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;" | base64
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

* Try to access application gateway IP in the browser and you will below the error message
    
    ![Image](https://github.com/user-attachments/assets/5f26931b-b116-4747-804c-da85aa32ab80 align="left")
    
    ![Image](https://github.com/user-attachments/assets/33ae7b59-c0b6-432d-86f0-80b1fceabb9c align="left")
    

## Build the second pipeline (Application).

* Build application pipeline
    
* ![Image](https://github.com/user-attachments/assets/6a2d7d9e-589b-410f-80c0-4c5a162b7dfc align="left")
    
* run the pipeline.
    
* It will ask for permission and approve it.
    
* ![Image](https://github.com/user-attachments/assets/e156d14f-8f97-4d6e-bb1d-39db605854ff align="left")
    
* Update the deployment.yaml file as below (SQL Connection)
    
* ![Image](https://github.com/user-attachments/assets/544e9fb5-e59c-47f2-af48-02d04950ac45 align="left")
    
* AKS deployment is waiting for approval
    
* ![Image](https://github.com/user-attachments/assets/acd32b2b-5194-4863-8c02-38f86419c6e0 align="left")
    

### Troubleshooting.

* I was getting an error `imagepullbackoff` and noticed that the `SQL connection` name was having a problem. To retrieve and update the string from Secret.
    
* Retrieve and Update the Connection String from the Secret.
    
    * Since you're using a Kubernetes secret for the database connection, check the secret value:
        
    
    ```bash
    kubectl get secret db-connection-secret -o jsonpath="{.data.connection-string}" | base64 --decode
    ```
    
* Pipeline status
    
* ![Image](https://github.com/user-attachments/assets/8111a79c-376f-4551-9674-a6b5c553ffdb align="left")
    
* Image status
    
* ![Image](https://github.com/user-attachments/assets/d684659b-7fb5-4fd0-a03c-f9a766a5c730 align="left")
    

### Update the image name in the manifest file.

![Image](https://github.com/user-attachments/assets/544e9fb5-e59c-47f2-af48-02d04950ac45 align="left")

Congratulations :-) the application is working and accessible.

* Application is accessible now.  
    
    ![Image](https://github.com/user-attachments/assets/1e298a29-52e7-48b7-972b-e355b90de3ad align="left")
    
* Try to create account in website.
    
* ![Image](https://github.com/user-attachments/assets/724fa414-c3d2-4d50-82dc-5ed9676eb054 align="left")
    
* Following are the cred to login into webpage
    
* ![Image](https://github.com/user-attachments/assets/28a139cc-9e6d-4eec-ac18-0e5214bec229 align="left")
    

## Pipeline for Cleanup Infra Setup.

* Following are the sequence to delete the setup using pipeline.
    
    * AKS Cluster
        
    * Application Gateway
        
    * Azure Container Registry
        
    * SQL Database
        
    * Agent VM
        
    * VNet
        
* Here is the 👉[Updated pipeline](https://github.com/mrbalraj007/Azure_DevOps_Projects/blob/main/Azure_DevOps_All_Projects/11_Real-Time-DevOps-Project_CI-CD_Terraform_ACR_Storage_WebApp_SQL/Pipeline/Destroy%20Infra.md)👈
    

## Environment Cleanup:

* As we are using Terraform, we will use the following command to delete `ssh_key`, `Vault` and `Storage account`.
    
* Run the terraform command.
    
    ```bash
    Terraform destroy --auto-approve
    ```
    
* I was getting below error message while deleting the setup
    
* ![Image](https://github.com/user-attachments/assets/81ae4575-01fb-47f2-8f4b-8207862394dc align="left")
    
* Rerun the destroy command again and it will delete `ResourceGroup` as well.
    
* ![Image](https://github.com/user-attachments/assets/86309505-ce7b-47d1-9459-4e4e9b3f194d align="left")
    

## Conclusion

This project showcases a strong CI/CD pipeline setup using Azure DevOps, Terraform, and various Azure services. By automating the deployment process, it ensures efficient, scalable, and secure deployment of a web application on a private AKS cluster. Using Terraform for infrastructure as code offers flexibility and easy management, making it a valuable approach for modern cloud-based applications.

**Ref Link:**

* [YouTube Link](https://www.youtube.com/watch?v=PDBoprlJfUw)
    
* [Standard and Basic SKU](https://learn.microsoft.com/en-us/answers/questions/1349124/azure-cannot-change-the-static-ip-address)
    
* [Terraform code for azurerm\_public\_ip](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/public_ip)
    
* [Terraform code for azurerm\_kubernetes\_cluster](https://registry.terraform.io/providers/hashicorp/azurerm/2.17.0/docs/resources/kubernetes_cluster)
    
* [AKS cluster\_node\_pool](https://registry.terraform.io/providers/hashicorp/azurerm/2.48.0/docs/resources/kubernetes_cluster_node_pool)
    
* [Kubernetes versions details](https://learn.microsoft.com/en-us/azure/aks/supported-kubernetes-versions?tabs=azure-cli#supported-version-list)
    
* [Billing FAQs](https://learn.microsoft.com/en-us/azure/devops/organizations/billing/billing-faq?view=azure-devops&viewFallbackFrom=vsts&tabs=new-nav)