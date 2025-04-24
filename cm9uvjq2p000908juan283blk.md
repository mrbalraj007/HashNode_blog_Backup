---
title: "Streamlining AWS Integration with GitHub Actions: A Secure and Scalable Approach Using OpenID Connect"
datePublished: Thu Apr 24 2025 04:40:24 GMT+0000 (Coordinated Universal Time)
cuid: cm9uvjq2p000908juan283blk
slug: streamlining-aws-integration-with-github-actions-a-secure-and-scalable-approach-using-openid-connect
cover: https://raw.githubusercontent.com/mrbalraj007/Learn_MkDocs_Documents/main/post/blog/assets/image-10.png
tags: authentication, aws, github, opensource, authorization, integration, roles, permissions, oidc, github-actions-1, generate-aws-access-key-and-secret-key, role-users-usersgroups-identityprovider, awsintegration

---

![alt text](https://raw.githubusercontent.com/mrbalraj007/Learn_MkDocs_Documents/main/post/blog/All_ScreenShot/image-10.png align="left")

## Objective

The project demonstrates how to integrate GitHub Actions with AWS to automate workflows, such as uploading Docker images to AWS Elastic Container Registry (ECR).

* Two approaches are explored:
    
    * Using manually generated AWS access keys and secret keys.
        
    * Using OpenID Connect (OIDC) for secure and temporary token-based authentication.
        

---

## Prerequisites

Before diving into this project, here are some skills and tools you should be familiar with:

* Terraform is installed on your machine.
    
* A GitHub account.
    
* A GitHub personal access token with the necessary permissions to create repositories.
    
* [Clone repository for terraform code](https://github.com/mrbalraj007/GitHub-Action-OIDCConnect.git)  
    
    > 💡 **Note:** *Replace resource names and variables as per your requirement in terraform.tfvars file as below*
    
    ```sh
        aws_region             = "us-east-1"
        github_repo_sub        = "repo:xxxxxxxxxxxxxx/GitHub-Action-OIDCConnect:*"    # Update here your GitHub account name and repo 
        web_identity_role_name = "github-actions-web-identity-role" # It will be created as part of Terraform, in case you want to use different then change it.
        web_identity_repo_sub  = "repo:xxxxxxxxxxxxxxxxx/GitHub-Action-OIDCConnect:ref:refs/heads/main" # Update here your GitHub account name and repo
        additional_policy_arn  = "arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess"
    ```
    
* **Set up your GitHub token**:
    
    * Create a new GitHub personal access token with the `repo` scope at https://github.com/settings/tokens.
        
    * Then set it as an environment variable (DO NOT commit your token to version control):
        
        ```bash
        # For Linux/macOS
        export GITHUB_TOKEN=your_github_token
        
        # For Windows Command Prompt
        set GITHUB_TOKEN=your_github_token
        
        # For Windows PowerShell (I used this one)
        # $env:GITHUB_TOKEN="your_github_token"
        $env:TF_VAR_github_token = "your-github-personal-access-token"
        ```
        
* **Test and verify with curl again in PowerShell terminal:**
    
    ```powershell
    $headers = @{
        Authorization = "token $env:TF_VAR_github_token"
    }
    Invoke-WebRequest -Uri "https://api.github.com/user" -Headers $headers
    ```
    
    * You should see your GitHub user info in JSON, **not** "Bad credentials".
        

---

## Key Points

### **Approach 1**: Manual Access Keys

* Create an IAM user in AWS.
    
* Generate access keys and secret keys.
    
* Configure these keys as repository secrets in GitHub.
    
* Use these secrets in GitHub Actions workflows to authenticate and perform operations on AWS.
    

### **Approach 2**: OpenID Connect (OIDC)

* Configure OpenID Connect as an identity provider in AWS.
    
* Assign roles to allow GitHub Actions to access AWS resources.
    
* Use the role's ARN in the GitHub Actions workflow for authentication.
    
* No need to manage or rotate access keys manually.
    
* Tools and Technologies Used
    

## GitHub Actions: For automating workflows.

* **AWS IAM**: For managing users, roles, and permissions.
    
* **AWS ECR**: As the target service for Docker image uploads.
    
* **OpenID Connect (OIDC)**: For secure, token-based authentication. Challenges
    

### **Manual Key Management**:

In the first approach, managing and rotating keys is cumbersome and prone to errors.

* **Configuration Errors**: Issues like incorrect indentation or missing permissions in the workflow file can cause failures.
    
* **Security Risks**: Using administrator access in IAM roles is not recommended in real-world scenarios.
    

## **Benefits of using OIDC**

* Improved Security: OIDC eliminates the need for long-term credentials, reducing the risk of key leakage.
    
* Ease of Maintenance: No need to rotate keys periodically with OIDC.
    
* **Scalability**: OIDC allows seamless integration across multiple repositories and branches.
    

## Step-by-Step Process

### Manual Access Keys Approach

* Create an IAM user in AWS with the required permissions.
    
* Generate access keys and secret keys.
    
* Add these keys as repository secrets in GitHub.
    
* Configure a GitHub Actions workflow to use these secrets for AWS operations.
    
    > ⚠️ **Important:** ⇒ Here is the [Updated YAML](https://github.com/mrbalraj007/GitHub-Action-OIDCConnect/blob/main/.github/workflows/AWSConnectionWithManual.yml) file formanually approach.
    

---

🚀 **I'll be demonstrating the second approach to setting up OIDC.** 🚀

### OpenID Connect Approach

#### Setting Up the OIDC in AWS

* I have created Terraform code to set up the OIDC, with roles and permissions automatically created.
    
* Once you [clone repo](https://github.com/mrbalraj007/GitHub-Action-OIDCConnect.git) then go to folder *"GitHub-Action-OIDCConnect/Terraform\_Code\_Infra\_setup"* and run the terraform command.
    
    ```bash
    $ cd GitHub-Action-OIDCConnect/Terraform_Code_Infra_setup
    
    $ ls
    oidc-setup.tf  provider.tf  terraform.tfstate  terraform.tfstate.backup  terraform.tfvars  variables.tf  web_identity_roles.tf
    ```
    

You need to run terraform using the following command:

```bash
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply 
# Optional <terraform apply --auto-approve>
```

---

![alt text](https://raw.githubusercontent.com/mrbalraj007/Learn_MkDocs_Documents/main/post/blog/All_ScreenShot/image.png align="left")

Once you run the Terraform command, we will verify the following things to ensure everything is set up properly using Terraform.

* Verify OpenID Connect as an identity provider in AWS.
    
* Verify assign a role and permission to the identity provider with specific permissions.
    
* Update secrets and variables in the GitHub Actions workflow to use the role's ARN for authentication.
    
* Ensure the workflow file includes the required permissions block for OIDC.
    
* Here is the 🗎 [updated YAML](https://github.com/mrbalraj007/GitHub-Action-OIDCConnect/blob/main/.github/workflows/AWSConnectionWithOIDC.yml) file for OIDC.
    

**Testing**

* Run the GitHub Actions workflow to verify AWS authentication and operations.
    
    ![alt text](https://raw.githubusercontent.com/mrbalraj007/Learn_MkDocs_Documents/main/post/blog/All_ScreenShot/image-6.png align="left")
    
    ![alt text](https://raw.githubusercontent.com/mrbalraj007/Learn_MkDocs_Documents/main/post/blog/All_ScreenShot/image-7.png align="left")
    
    ![alt text](https://raw.githubusercontent.com/mrbalraj007/Learn_MkDocs_Documents/main/post/blog/All_ScreenShot/image-8.png align="left")
    
* Debug and fix any errors, such as missing permissions or incorrect configurations.
    

## Environment Cleanup:

### To delete `OIDC, Roles and Permissions`

* Go to the directory `GitHub-Action-OIDCConnect/Terraform_Code_Infra_setupand` ,and run the following command to delete the cluster.
    
    ```sh
      terraform destroy --auto-approve
    ```
    

![alt text](https://raw.githubusercontent.com/mrbalraj007/Learn_MkDocs_Documents/main/post/blog/All_ScreenShot/image-9.png align="left")

## **Conclusion**

The project shows the benefits of using OpenID Connect instead of manual access keys for integrating GitHub Actions with AWS. OIDC offers a more secure, scalable, and maintenance-free solution, making it the preferred choice for real-world applications. By using OIDC, teams can concentrate on building and deploying applications without the hassle of managing credentials.

**Ref Link:**

* [configuring-openid-connect-in-amazon-web-services](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services)
    
* [GitHub Action Market Place- Configure-aws-credentials](https://github.com/aws-actions/configure-aws-credentials)
    
* [YouTube Vidoes](https://www.youtube.com/watch?v=uNbzEGzyDjc&list=PLJcpyd04zn7pdlV_nLLx349jl2CSuG50q&index=10)