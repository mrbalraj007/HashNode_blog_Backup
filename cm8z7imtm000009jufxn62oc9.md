---
title: "Seamlessly Integrate On-Premises Active Directory with Azure AD"
datePublished: Wed Apr 02 2025 00:46:51 GMT+0000 (Coordinated Universal Time)
cuid: cm8z7imtm000009jufxn62oc9
slug: seamlessly-integrate-on-premises-active-directory-with-azure-ad
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1743553940719/4972c004-340c-4ffc-8bf0-8a494193fd92.png
tags: azure, migration, integration, active-directory, azure-migration-services-azure-cloud-consultants-azure-managed-services-azure-consulting-partner, active-directory-domain-service, on-premises-to-azure-migration

---

## Prerequisites

1. **Azure Subscription**: Ensure you have an active Azure subscription.
    
2. **Azure AD Tenant**: Create an Azure AD tenant if you don't have one.
    
3. **Azure AD Connect**: Download and install Azure AD Connect on a server in your on-premises environment.
    
4. **On-Premises Active Directory**: Ensure your on-premises Active Directory domain is functional and accessible.
    
5. **On-Premises Active Directory**: Ensure your on-premises Active Directory domain has .net 4.7.2 and TLS 1.2 version installed
    
6. [Terraform code for Infra setup](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/AWS-SSO-Integrate-with_Azure_AD/Terraform_Code_Azure_VM)  
    
    > **Note**: Replace resource names and variables as per your requirement in terraform code
    
    * Update values in`terraform.tfvars`
        

## Step-by-Step Guide

### Setting Up the Infrastructure

*I have created Terraform code to automatically set up Virtual Machines and install Active Directory, along with creating users, groups, and service accounts.*

> **Note**\--&gt; Virtual Machine will take approx 10 to 15 min to install the all required setup.

* ⇒ Virtual machines will be created named as `"ad-server"`
    
* ⇒ Active Direcotry install
    
* ⇒ Will create users, Groups and Service accounts
    
* ⇒ Storage Acccount & Blob Setup
    

First, we'll create the necessary virtual machines using `terraform` code.

* Below is a terraform Code:
    
* Once you [clone repo](https://github.com/mrbalraj007/Azure_DevOps_Projects/tree/main/AWS-SSO-Integrate-with_Azure_AD/Terraform_Code_Azure_VM) and run the terraform command.
    
    ```bash
    $ ls -l
      -rw-r--r-- 1 bsingh 1049089  2309 Mar 26 20:29 Configure-AD-Users.ps1
      -rw-r--r-- 1 bsingh 1049089  7705 Mar 31 12:37 Configure-AD-Users-and-Groups.ps1
      -rw-r--r-- 1 bsingh 1049089  6842 Mar 26 20:29 Install-AD.ps1
      -rw-r--r-- 1 bsingh 1049089  6912 Mar 31 11:31 main.tf
      -rw-r--r-- 1 bsingh 1049089   492 Mar 26 17:14 terraform.tfvars
    ```
    
* You need to run the terraform command.
    
    * Run the following command.
        
    
    ```bash
    terraform init
    terraform fmt
    terraform validate
    terraform plan
    terraform apply 
    # Optional <terraform apply --auto-approve>
    ```
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554113418/b41db473-c7e3-4e42-bccc-b2e1d0cac961.png align="center")

  
Once you run the Terraform command, we will verify the following things to ensure everything is set up correctly through Terraform.

### Inspect the `C:\WindowsAzure` logs:

Once connected to VM then you can check the status of the `CustomScript` script by inspecting the log files

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554126545/0ddca767-10da-4b60-88e4-240f286dff17.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554132161/3e5515d5-1799-4802-92ba-5fcd999ca701.png align="center")

  

### Verify the Installation

#### Verify `Virtual Machine` Status in Azure Console  

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554157913/956973c3-6dd0-48e7-ac56-9c0cfcbe41ef.png align="center")

### Verify `Users, Groups` and `Service account` in Active Directory.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554177957/f0cd2afc-678d-4b83-a574-8cb393b6a663.png align="center")

###   
Permission to service account in Active Directory.

* Below are the permissions need to assign to service account in Active Directory.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554184911/2c3cf46f-e498-4217-bb4f-19764084a185.png align="center")
    
      
    

### Verify `storage account` and `blob`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554193945/c2d23cd1-b485-43a9-a047-a960c86192e1.png align="center")

###   

### To disable `Internet Explorer Enhanced Security Configuration`.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554202699/f08e4875-b2e1-483c-8c86-4edd5e57fe47.png align="center")

###   

### To Verify TLS Status

#### To check whether TLS 1.2 is enabled on a Windows system using PowerShell, you can run the following command:

```powershell
[Net.ServicePointManager]::SecurityProtocol.HasFlag([Net.SecurityProtocolType]::Tls12)
```

If TLS 1.2 is enabled, this will return True. Otherwise, it will return False.

#### Alternative Method: Checking the Registry

You can also check the Windows registry to see if TLS 1.2 is explicitly enabled:

```powershell
$registryPath = "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client"
$enabled = Get-ItemProperty -Path $registryPath -Name "Enabled" -ErrorAction SilentlyContinue

if ($enabled -and $enabled.Enabled -eq 1) {
    Write-Output "TLS 1.2 is enabled."
} else {
    Write-Output "TLS 1.2 is NOT enabled."
}
```

* Verified that TLS is not enabled on the Server.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554265122/5e6a999c-5227-4822-83b4-9ecf77be23ea.png align="center")
    
      
    

### To enable TLS 1.2 on both Client and Server in Windows

#### To enable TLS 1.2 on both Client and Server in Windows, use the following PowerShell command:

```powershell
# Enable TLS 1.2 for Client
New-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client" -Force | Out-Null
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client" -Name "Enabled" -Value 1 -Type DWord
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client" -Name "DisabledByDefault" -Value 0 -Type DWord

# Enable TLS 1.2 for Server
New-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" -Force | Out-Null
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" -Name "Enabled" -Value 1 -Type DWord
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" -Name "DisabledByDefault" -Value 0 -Type DWord

# Enable TLS 1.2 for .NET Framework (for older apps)
New-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319" -Name "SchUseStrongCrypto" -Value 1 -PropertyType DWord -Force | Out-Null
New-ItemProperty -Path "HKLM:\SOFTWARE\Wow6432Node\Microsoft\.NETFramework\v4.0.30319" -Name "SchUseStrongCrypto" -Value 1 -PropertyType DWord -Force | Out-Null

Write-Output "TLS 1.2 has been enabled. A system restart is recommended for changes to take effect."
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554284412/e3e271a3-0f57-4db9-94de-463f068c0b34.png align="center")

  
***Explanation:*** \*- Enables TLS 1.2 for Client and Server in the SCHANNEL registry settings.

* Enables strong cryptography in .NET Framework, so applications use TLS 1.2 by default.
    
* A system restart is required to fully apply the changes.\*
    
* Verified that `TLS status` on the Server.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554616683/1963ae4c-5bb3-4bc9-9b85-0f26c0fad3d5.png align="center")
    
      
    

### Step 1: Prepare Azure AD

1. **Login to Azure Portal**: Go to [Azure Portal](https://portal.azure.com) and log in with your Azure account.
    
2. **Create Azure AD Tenant**:
    
    * Navigate to `Microsoft Intra ID` &gt; `Create a directory`.
        
    * Follow the prompts to create a new directory.
        
3. **Create a new admin user for migration**:
    
    * Will create a new user in Azure AD and assign the `global administrator` rights.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554306046/34418a56-3417-4e92-b6a7-81f2dc2eea9f.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554312878/74070db0-7a2b-4b67-b642-8e53fe96310c.png align="center")
        
          
        

### Step 2: Configure Azure AD Connect

1. **Download Azure AD Connect**:
    
    * Go to the [Azure AD Connect download page](https://www.microsoft.com/en-us/download/details.aspx?id=47594) and download the latest version.
        
2. **Install Azure AD Connect**:
    
    * Run the installer on a server in your on-premises environment.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554331313/50bbc9b4-d321-4b2c-89d7-93f16790b61e.png align="center")
        
          
        
    * Choose the `Customize` option for a simple setup.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554338665/a97ecb38-adc4-45de-b17f-335f792f7d26.png align="center")
        
          
        
    * Leave as it is and click on install
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554370286/e8de8b9e-614e-491c-9426-69f0e74f4820.png align="center")
        
          
        
    * Select the Sign On Method
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554384604/53704c0d-4181-4308-ba0b-04941fb03381.png align="center")
        
          
        
3. **Configure Azure AD Connect**:
    
    * During the setup, you will be prompted to enter your Azure AD and on-premises AD credentials.
        
    * Type the user which created in Step 1 which has `global administrator` rights select the Sign On Method
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554396402/0e62f44c-c775-438b-a3d0-e41dcf1c64bf.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554403333/7ef4d09a-7fcd-479e-92e1-ce2885e24569.png align="center")
        
          
        
    * Select the [`Singh.org.au`](http://Singh.org.au) domain for synchronization and click on add directory.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554410282/baa8d33f-8d2c-474d-8a66-275469380b99.png align="center")
        
          
        
    * Select `use existing AD Account` and type `service account` details.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554419488/a8b57ca5-e700-4d44-866c-f49483087b78.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554425086/e297649f-6656-435a-96c5-c2d026f6435f.png align="center")
        
          
        
    * click on `Continue without matching all UPN suffixes to verified domains`
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554431376/b1bbf316-417c-490d-8c14-91b6b35704d3.png align="center")
        
          
        
    * Click on `Sync selected domains and OUs`
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554438910/90d96e2f-d81b-45be-af83-ffae115588e3.png align="center")
        
          
        
    * leave the default setting and click next.
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554446229/1917361f-61f8-4b50-bdc3-1a3507c42a53.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554451325/1e5325cb-b85c-4a93-91ee-d5f7fd27458a.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554456488/f11aabf8-35ae-4c27-b44c-27f908b215fb.png align="center")
        
          
        
    * Choose the synchronization options that best fit your environment (e.g., password hash synchronization, pass-through authentication).
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554463501/340b3597-f8f4-4297-86d9-c86d4d92602c.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554468581/e59778a7-6cce-4824-9b24-450b4606dcd9.png align="center")
        
          
        

### Step 3: Verify Synchronization

1. **Initial Sync**:
    
    * Once the setup is complete, Azure AD Connect will perform an initial synchronization.
        
    * You can monitor the sync status in the Azure AD Connect tool.
        
2. **Verify Users in Azure AD**:
    
    * Go to `Azure Active Directory` &gt; `Users` in the Azure Portal.
        
    * Verify that the on-premises AD users are listed in Azure AD.
        
    * Users Status
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554476029/64286bad-803b-43ce-85f3-34b269cfdce9.png align="center")
        
          
        
    * Group Status
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554487385/50b46b4f-9299-4aae-b46a-f9204bb4277f.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554493108/f1c9445f-2046-4245-b239-e4c2f4e5ec35.png align="center")
        
          
        

### Step 4: Configure Additional Settings (Optional)

1. **Password Writeback**:
    
    * If you want to enable password writeback, configure it in the Azure AD Connect tool.
        
    * This allows users to change their passwords in Azure AD and have them written back to the on-premises AD.
        
2. **Single Sign-On (SSO)**:
    
    * Configure SSO to allow users to sign in once and access both on-premises and cloud resources.
        

## Error and Troubleshooting.

* I could not log in with a user account and noticed that users were getting a permission issue.
    

**Fix**

* I used a service account to configure the AD migration but didn't give it permission at the domain level. So, I granted the necessary permissions to the service account at the domain level and then ran the following PowerShell command to sync the changes and verify them in the service manager.  
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554522846/76b2695d-2c68-456f-9356-268bbcade3ca.png align="center")
    

```sh
Start-ADSyncSyncCycle -PolicyType Delta
Start-ADSyncSyncCycle -PolicyType initial
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1743554554480/703db857-cf46-4a61-8d9c-2e67df88c753.png align="center")

### Delete Azure Security Group (Optional)

```sh
az ad group delete --group 'GroupName' --verbose
```

### Conclusion

By following these steps, you will have successfully synchronized your on-premises Active Directory with Azure AD. This setup ensures that your users can access both on-premises and cloud resources with a single set of credentials.

**Ref Link**

* [Sync on-premises Active Directory to Azure Active Directory with Azure AD Connect](https://www.codetwo.com/admins-blog/how-to-sync-on-premises-active-directory-to-azure-active-directory-with-azure-ad-connect/)