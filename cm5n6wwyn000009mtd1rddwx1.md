---
title: "YouTube Clone: Automating CI/CD with Azure DevOps: Blue-Green Deployment and Self-Hosted Agents on Azure VM Scale Sets"
datePublished: Wed Jan 08 2025 00:57:37 GMT+0000 (Coordinated Universal Time)
cuid: cm5n6wwyn000009mtd1rddwx1
slug: youtube-clone-automating-cicd-with-azure-devops-blue-green-deployment-and-self-hosted-agents-on-azure-vm-scale-sets
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1736297447919/8ecac13b-4b46-43d0-8623-466344301c1f.png
tags: webapps, security, npm, devops, youtube, terraform, pipeline, azure-pipelines, artifacts, devops-articles, stages, devops-devopsarticles-devopstools-aws-amazonwebservices-terraform-kubernetes-ansible-git-d0cker-jenkins-prometheus-splunk-grafana-nagios, web-apps-development-services, azure-repo, mvss

---

![App_YouTube Clone_Compress](https://github.com/user-attachments/assets/c8637593-efce-42b6-ac95-ef90f4272772 align="left")

## Overview

This project provides a comprehensive guide to automating Continuous Integration and Continuous Deployment (CI/CD) using Azure DevOps. It covers two main topics: Blue-Green Deployment using Azure DevOps Release Pipelines and setting up Self-Hosted Agents using Azure Virtual Machine Scale Sets (VMSS). The project uses a YouTube clone application as a practical example to demonstrate the concepts and steps involved.

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

* \[x\] [Clone repository for terraform code](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/Azure_DevOps_All_Projects/03.1_Real-Time-DevOps-Project_CI-CD_VMSS_Storage_webapp-YouTube_Clone)  
    **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * Update `terraform.tfvars`.
        
* \[x\] [App Repo (YouTube Clone)](https://github.com/piyushsachdeva/Youtube_Clone)
    
* \[x\] **Azure Account**: You’ll need an Azure account to create resources like virtual Machines, Storage, and manage pipelines.
    
* \[x\] **Terraform Knowledge**: Familiarity with Terraform is needed to set up, manage, and remove infrastructure.
    
* \[x\] **GitHub**: Experience with GitHub for version control and managing repositories.
    
* \[x\] **Command-Line Tools**: Basic comfort with using the command line for managing infrastructure and services.
    
* \[x\] **Basic CI/CD Knowledge**: Some understanding of Continuous Integration and Deployment is recommended.
    
* \[x\] **Linux VM**: Docker must be installed on a Linux virtual machine to run containers.
    

## Azure DevOps Release Pipelines: Blue-Green Deployment

This section focuses on setting up Azure DevOps release pipelines to automate the deployment process. It explains how to create a release pipeline using the GUI-based editor, configure continuous deployment triggers, and implement deployment gates for additional control. The blue-green deployment strategy is highlighted, showing how to use deployment slots to minimize downtime during deployments.

## Azure DevOps Self-Hosted Agents: Virtual Machine Scale Sets (VMSS)

This section delves into the setup and use of self-hosted agents on Azure VMSS. It compares Microsoft-hosted and self-hosted agents, explaining the benefits and use cases. Detailed steps are provided to set up self-hosted agents on VMSS, register the agents with Azure DevOps, and use them in pipelines. The section also covers the benefits of using VMSS, such as automatic scaling and high availability.

## Key Points

* **Technologies Used**: Azure DevOps, Azure Virtual Machine Scale Sets (VMSS)
    
* **Self-Hosted Agents**: Dedicated agents for running CI/CD pipelines
    
* **Automatic Scaling**: Provisioning and de-provisioning VMs based on load
    
* **Extensions and Applications**: Installing necessary software on VMs
    
* **Service Connections**: Secure authentication for Azure resources
    
* **Automated Deployment**: Demonstrates how to automate the deployment process using Azure DevOps release pipelines.
    
* **Reduced Downtime**: Ensures minimal downtime during deployments with the blue-green deployment strategy.
    
* **Enhanced Control**: Provides additional control over the deployment process with deployment gates.
    
* **Scalability**: Allows for easy scaling of the application using Azure App Service and deployment slots.
    
* **Custom Software Installation**: Enables automatic installation of custom software on self-hosted agents.
    
* **High Performance**: Provides high performance and specific resource requirements with self-hosted agents.
    
* **Network Security**: Ensures secure handling of sensitive data by keeping it within a secure network.
    
* **Cost Efficiency**: Optimizes resource management and costs with automatic scaling based on demand.
    

## Setting Up the Infrastructure

I have created Terraform code to set up the entire infrastructure, including the automatic installation of necessary software, and tools, and the creation of the web app.

Below is a terraform Code:

Once you [clone repo](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/Azure_DevOps_All_Projects/03.1_Real-Time-DevOps-Project_CI-CD_VMSS_Storage_webapp-YouTube_Clone) and run the terraform command.

```bash
$ ls -l
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
dar--l          26/12/24   7:16 PM                pipeline
dar--l          23/12/24   3:38 PM                scripts
-a---l          25/12/24   2:31 PM            600 .gitignore
-a---l          26/12/24   9:29 PM           6571 VM_ACR.tf
-a---l          26/12/24   9:29 PM            892 main.tf
-a---l          26/12/24   9:29 PM            567 output.tf
-a---l          26/12/24   9:29 PM            269 provider.tf
-a---l          26/12/24   9:30 PM            223 terraform.tfvars
-a---l          26/12/24   9:30 PM            615 variable.tf
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

---

### Step-by-Step Description

#### 1\. Project Setup

* **Clone the Repository**: Clone the project repository from the source control.
    
* **Import into Azure DevOps**: Import the project into Azure DevOps and set up the necessary service connections.
    

#### 2\. Create a Virtual Machine Scale Set

* **Azure Portal**: Log in to the Azure portal and search for "Virtual Machine Scale Sets".
    
* **Create VMSS**: Click on "Create" and fill in the required details such as subscription, resource group, and region.
    
* **Select VM Size**: Choose an appropriate VM size (e.g., Standard B1s) and configure scaling options.
    
* **Networking and Management**: Configure networking options and set the upgrade mode to manual.
    

#### 3\. Configure Extensions and Applications

* **Add Extensions**: Navigate to the "Extensions and Applications" section in the VMSS settings.
    
* **Install Azure DevOps Pipeline Agent**: Add the Azure DevOps pipeline agent extension to the VMSS.
    
* **Custom Scripts**: Optionally, add custom scripts to install additional software (e.g., npm, Azure CLI).
    

#### 4\. Set Up Agent Pool in Azure DevOps

* **Project Settings**: Go to the project settings in Azure DevOps and navigate to the "Agent Pools" section.
    
* **Add Pool**: Create a new agent pool and select "Azure Virtual Machine Scale Set" as the pool type.
    
* **Configure Pool**: Select the appropriate Azure subscription and VMSS, and configure the scaling options.
    

#### 5\. Use Self-Hosted Agent in Pipeline

* **Edit Pipeline**: Edit the existing pipeline to use the newly created self-hosted agent pool.
    
* **Specify Pool**: Update the pipeline configuration to specify the self-hosted agent pool.
    
* **Run Pipeline**: Trigger the pipeline to run using the self-hosted agent.
    

#### 6\. Testing and Debugging

* **Monitor Agents**: Check the status of the agents in the Azure DevOps agent pool section.
    
* **Debug Issues**: Use Azure DevOps logs and VMSS monitoring tools to debug and fix any issues.
    

### Detailed Steps

#### Step 1: Project Setup

1. **Clone the Repository**:
    
    * Use Git to clone the repository containing the project files.
        
    * Command: `git clone` [`https://github.com/piyushsachdeva/Youtube_Clone`](https://github.com/piyushsachdeva/Youtube_Clone)
        
2. **Import into Azure DevOps**:
    
    * Navigate to Azure DevOps and create a new project.
        
    * Import the cloned repository into the Azure DevOps project.
        

#### Step 2: Create a Virtual Machine Scale Set

1. **Azure Portal**:
    
    * Log in to the Azure portal.
        
    * Search for "Virtual Machine Scale Sets" in the search bar
        
    * click on Azure CLI and run the following command
        
        ```sh
        az vmss show --resource-group <ResourceGroupName> --name <VMScaleSetName> --output table
        ```
        
    
    ![image-68](https://github.com/user-attachments/assets/622665b8-2f20-443d-ab93-18b77972a70c align="left")
    
    * After creating your scale set, navigate to your scale set in the Azure portal and verify the following settings:
        
        * Upgrade policy - Manual
            
        * Scaling - Manual scale
            
            ![image-69](https://github.com/user-attachments/assets/99af45ec-1754-4db2-9fa2-4ca33fa14374 align="left")
            
            ![image-70](https://github.com/user-attachments/assets/4c8d951e-f097-4d5b-9d75-c062425fc634 align="left")
            
        
        **Important:** *Azure Pipelines does not support instance protection. Make sure you have the scale-in and scale-set actions instance protections disabled.*
        

#### Step 3: Configure Extensions and Applications

1. **Add Extensions**:
    
    * Navigate to the "Extensions and Applications" section in the VMSS settings.
        
    * Click on "Add" to install extensions.
        
        ![image-78](https://github.com/user-attachments/assets/b0ae7aca-6774-4fee-89eb-be633d057efa align="left")
        
    
    **Note:** *Part of provisioning the server includes creating a storage account, setting up a container, and uploading the script to the "Script container."*
    
2. **Custom Scripts**:
    
    * Optionally, add custom scripts to install additional software (e.g., npm, Azure CLI).
        
    * Example:
        
        * Search for "Custom Script for Linux" extension. Upload a script file to install the software:
            
            ![image-79](https://github.com/user-attachments/assets/ca39a85a-fd6e-4820-bd40-82d190a418ac align="left")
            
            ![image-80](https://github.com/user-attachments/assets/ad4fdfe2-9f9b-4153-be40-d386020801a2 align="left")
            
            ![image-81](https://github.com/user-attachments/assets/874b1c7e-2e49-49b0-aff4-7fde56c64bc2 align="left")
            
            ![image-82](https://github.com/user-attachments/assets/d78d208f-c4d4-453f-89a6-049550af4af5 align="left")
            
            ![image-83](https://github.com/user-attachments/assets/71d66273-a0d4-4dbc-b44e-6860a101681c align="left")
            
            ![image-84](https://github.com/user-attachments/assets/be60f556-0dff-499b-a91a-65b7de6dd8e5 align="left")
            
            ![image-85](https://github.com/user-attachments/assets/bf7a5000-eea9-492c-83cc-8f4ac2bbee2e align="left")
            
            ![image-86](https://github.com/user-attachments/assets/4132edf1-4351-444b-a44b-eae9a440c44e align="left")
            
            ![image-87](https://github.com/user-attachments/assets/22c016d4-a8b7-400f-92e2-24599bcbbbac align="left")
            
            ![image-88](https://github.com/user-attachments/assets/6ceeb8f7-6462-42f9-9573-c814a5d40614 align="left")
            
            ![image-89](https://github.com/user-attachments/assets/306bf329-5b92-461a-ae6d-d4295706553d align="left")
            
            \- if the above script failed then try with this one only  
            `#!/bin/bash apt-get update && apt-get install -y npm`
            

#### Step 4: Set Up Agent Pool in Azure DevOps

1. **Project Settings**:
    
    * Go to the project settings in Azure DevOps.
        
    * Navigate to the "Agent Pools" section.
        
2. **Add Pool**:
    
    * Create a new agent pool and select "Azure Virtual Machine Scale Set" as the pool type.
        
    * Example:
        
        * Pool Name: `devops-demo_vm`
            
        * Azure Subscription: Select the appropriate subscription
            
        * VMSS: Select the created VMSS
            
        * Configure scaling options (e.g., maximum number of agents, idle instances)
            
3. **Install Azure DevOps Pipeline Agent**:
    
    * Add the Azure DevOps pipeline agent extension to the VMSS.
        
    * This extension allows the VMSS to act as a self-hosted agent for Azure DevOps pipelines.
        
    * Navigate to your Azure DevOps Project settings, select Agent Pools under Pipelines, and select Add Pool to create a new agent pool.
        
    * VMSS UI
        
    * Click on Instances and notice that latest Model is showing `No`.
        
    * **Note**: - "Azure Pipeline Agent for Linux"
        
        * It will take 5-10 minutes for the VMSS connection to become active.
            
        * Go to azure DevOps Portal and noticed that agent is online and accessible now.
            
            ![image-77](https://github.com/user-attachments/assets/55e08ca8-81f7-491c-bcbb-31996791d79b align="left")
            

#### Step 5: Build the Pipeline

1. **Edit Pipeline**:
    
    * Click on pipeline and select `Azure Repos Git (YAML)`
        
    * it will generate the basic structure.
        

#### Step 6: Use Self-Hosted Agent in Pipeline

1. **Edit Pipeline**:
    
    * Edit the existing pipeline to use the newly created self-hosted agent pool.
        
    * Example:
        
        * Update the pipeline YAML file to specify the agent pool:
            
            ```yaml
            pool:
              name: devops-demo_vm
            ```
            
        * 👉[Complete Pipeline](https://github.com/mrbalraj007/Azure_DevOps_Projects/blob/main/Azure_DevOps_All_Projects/03.1_Real-Time-DevOps-Project_CI-CD_VMSS_Storage_webapp-YouTube_Clone/Pipeline/Updated_CI.md)👈
            
2. **Run Pipeline**:
    
    * Trigger the pipeline to run using the self-hosted agent.
        
    * Monitor the pipeline execution to ensure it uses the self-hosted agent.
        

#### Step 7: Testing and Debugging

1. **Monitor Agents**:
    
    * Check the status of the agents in the Azure DevOps agent pool section.
        
    * Ensure the agents are online and ready to execute pipeline jobs.
        
2. **Debug Issues**:
    
    * Use Azure DevOps logs and VMSS monitoring tools to debug and fix any issues.
        
    * Example: If a pipeline step fails due to missing software, use custom scripts to install the required software on the VMSS.
        

#### Step 8: Verify the Artifacts

1. **Verify**:
    
    * Artficat will be produced once the pipeline is executed as below.
        
        ![image-17](https://github.com/user-attachments/assets/882ee7fa-7270-4de3-aba8-4bd1d3b8e3e5 align="left")
        

#### Step 9: Create WorkItem

1. **Create WorkItem**:
    
    * In work Iteams, change the priority from `P2` to `P1`.
        
        ![image-25](https://github.com/user-attachments/assets/9aa7cc8e-2d9b-4b58-a216-00ca2908cb68 align="left")
        
2. **Create Query**:
    
    * Will create a query and save it in `Shared Queries`
        
        ![image-22](https://github.com/user-attachments/assets/b7a5bef5-b9b8-403e-ad86-60a1fb40b60a align="left")
        
        ![image-23](https://github.com/user-attachments/assets/3986998d-d102-4a88-ad9e-377c9b5fbc66 align="left")
        
        ![image-24](https://github.com/user-attachments/assets/6fcb2bc0-ed21-4a87-a652-db8cfdd9cecc align="left")
        
3. **Create Chart in query**:
    

#### Step 10: Create a slot in WebApp

1. **Slot in Webapp**:
    
    * Create a slot:
        
    * Azure UI &gt; Web App &gt; Deployment Slot
        
        ![image-40](https://github.com/user-attachments/assets/bcfacc61-1af6-4969-bf7f-8b926a792e4d align="left")
        
        ![image-41](https://github.com/user-attachments/assets/ddedb32d-6c51-4254-bb73-f314dcebc377 align="left")
        
        ![image-42](https://github.com/user-attachments/assets/c4061955-fcad-49d4-8a75-ee1df827b540 align="left")
        
        ![image-43](https://github.com/user-attachments/assets/829934d5-d398-49d5-aa1a-acd5be18f2c6 align="left")
        

#### Step 11: Configure `Release Pipeline (CD)`

1. **To Configure the Dev Stage in CD pipeline**:
    

* 1.1. **Add Artifact & GateCheck in CD pipeline**:
    
    * Click on Release under pipelines and search as follows.
        
    * add artifact
        
    * Enable auto-trigger CD pipeline
        
    * Configure PreDeployment stage  
        
    * Configure the GateCheck in the pipeline
        
    
    1.2. **Configure the Job**
    
    * Configure the Job in the pipeline
        
        ![image-36](https://github.com/user-attachments/assets/018daaec-35d7-441d-a275-360101066628 align="left")
        
        ![image-37](https://github.com/user-attachments/assets/461ddb20-c9c8-45cf-a38c-3e063c1a848f align="left")
        
        ![image-38](https://github.com/user-attachments/assets/c8843c1c-48ab-4b86-a435-6d3a2703ba99 align="left")
        
    
    1.3 **Configure the slot Option**
    
    * Click `Save`
        

2. **To Configure the Prod Stage in CD pipeline**:
    

* To add Production stage.
    
    * clone the existing stage.
        
    * To add pre stage for the Production stage.
        
    * Change the slot from staging to prod.
        

#### Step 12: Update the query and workIteam in the pipeline

1. **To Configure query and workIteam in CD pipeline**:
    

* run the pipeline and Cd job would be failed because we have set the workitem query set to `doing` while it should be in `done` state.
    
* also, we need to update the query as below:
    
* Add security in query:
    
* change state from doing to done in workitem.
    

2. **ReRun the pipeline again**:
    
    * CI passed
        
        ![image-58](https://github.com/user-attachments/assets/eec86c76-b7e8-49c3-a130-2321de8565cb align="left")
        
    * CD- also passed
        
        ![image-59](https://github.com/user-attachments/assets/13fb2dda-ef18-46ca-bc16-7fd91f350f4c align="left")
        
        ![image-60](https://github.com/user-attachments/assets/1a4a2b07-7833-4160-b0ca-9376fc0b2e37 align="left")
        

* Website is accessible on staging.
    
* It's asking for approval for Prod Environment.
    
* Application is also accessible and working fine in Prod.  
    

3. **Swap the slot**:
    
    * swap the from UI
        
    * Current:
        
        ![image-66](https://github.com/user-attachments/assets/b1acc29e-c73e-4055-8759-8cca7043f455 align="left")
        
        ![image-67](https://github.com/user-attachments/assets/14acc970-1b64-4264-bb9a-b17c0f1ef025 align="left")
        

### Advantages of Using This Project

* **Automated Deployment**: The project demonstrates how to automate the deployment process using Azure DevOps release pipelines.
    
* **Reduced Downtime**: Blue-green deployment strategy ensures minimal downtime during deployments by using deployment slots.
    
* **Enhanced Control**: Deployment gates provide additional control over the deployment process, ensuring only approved changes are deployed to production.
    
* **Scalability**: Using Azure App Service and deployment slots allows for easy scaling of the application based on demand.
    

### Impact of Using This Project

* **Improved Efficiency**: Automating the deployment process saves time and reduces the risk of human error.
    
* **Increased Reliability**: Deployment gates and blue-green deployment strategies ensure that only tested and approved changes are deployed to production, increasing the reliability of the application.
    
* **Faster Time-to-Market**: Continuous deployment enables faster release cycles, allowing new features and updates to reach users more quickly.
    
* **Better User Experience**: Reduced downtime during deployments ensures a better user experience, as the application remains available during updates.
    

## Environment Cleanup:

* As we are using Terraform, we will use the following command to delete
    
    * run the terraform command.
        

```bash
Terraform destroy --auto-approve
```

### Conclusion

By following the steps outlined in this project, users can automate their CI/CD processes, improve control over deployments, and ensure reliable and scalable application updates. The use of blue-green deployment and self-hosted agents further enhances the user experience by minimizing downtime and providing greater control and security. This project serves as a valuable resource for anyone looking to implement DevOps practices in their workflow.

**Ref Link:**

* [VMSS Setup](https://www.youtube.com/watch?v=xO1RG7Cc0N8&list=PLl4APkPHzsUXseJO1a03CtfRDzr2hivbD&index=10)
    
* [CI-CD Pipeline Video](https://www.youtube.com/watch?v=acJErWFS15w&list=PLl4APkPHzsUXseJO1a03CtfRDzr2hivbD&index=6)
    
* [Install-node](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-20-04)
    
* [Install-node-js-and-npm-on-ubuntu](https://www.geeksforgeeks.org/how-to-install-node-js-and-npm-on-ubuntu/)
    
* [Scale-set-agents setup](https://learn.microsoft.com/en-us/azure/devops/pipelines/agents/scale-set-agents?view=azure-devops)