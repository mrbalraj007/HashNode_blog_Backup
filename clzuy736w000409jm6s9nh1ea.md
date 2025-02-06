---
title: "Mastering Terraform Linters: A Comprehensive Guide to TFLint for Secure and Efficient IaC"
datePublished: Thu Aug 15 2024 07:17:30 GMT+0000 (Coordinated Universal Time)
cuid: clzuy736w000409jm6s9nh1ea
slug: mastering-terraform-linters-a-comprehensive-guide-to-tflint-for-secure-and-efficient-iac
tags: help, documentation, devops, terraform, devsecops, devops-articles, terraform-cloud, devops-journey, tflint

---

In the world of Infrastructure as Code (IaC), Terraform is a popular tool that lets you define and manage your cloud infrastructure through code. However, like any code, your Terraform configurations need to be clean, error-free, and follow best practices. That's where TFLint comes in.

## What is TFLint?

TFLint is a powerful linter specifically designed for Terraform. A linter is a tool that analyzes your code to identify potential errors, bugs, stylistic issues, and deviations from best practices. TFLint helps you maintain high-quality Terraform configurations by catching mistakes early and providing recommendations for improvement.

## Why TFLint?

When working with Terraform, it's essential to maintain clean and efficient code. TFLint is a game-changer in this regard, offering insights that help avoid common pitfalls and enhance the quality of your infrastructure as code.

## Key Features of TFLint:

**Error Detection**: TFLint detects common errors in your Terraform code before you even run `terraform plan` or `terraform apply`.

**Best Practices**: It enforces best practices by checking your code against a set of predefined rules, ensuring your infrastructure is secure, efficient, and maintainable.

**Extensible**: TFLint is highly customizable. You can extend it with plugins and create custom rules to suit your specific needs.

**Integration**: It easily integrates with CI/CD pipelines, helping automate the process of checking your Terraform code for errors.

## TFLint vs. Terraform Plan: What’s the Difference?

It's important to differentiate between TFLint and the Terraform command terraform plan, as both serve distinct purposes:

### **Terraform Plan**:

**Purpose**: Shows the changes that will be made to your infrastructure.

**Function**: Shows you what actions Terraform will take (e.g., creating, updating, or deleting resources).

**When to Use**: Before applying your Terraform code to see what changes will occur in your infrastructure.

**Focus**: Infrastructure changes and resource management.

**Outcome**: Provides a detailed execution plan without making any changes to your infrastructure.

### **TFLint**:

**Purpose**: Lints (checks) your Terraform code for errors, misconfigurations, and best practice violations.

**Function**: Analyzes the code itself without interacting with the actual infrastructure.

**When to Use**: During the development process to ensure your Terraform files are clean, consistent, and follow best practices.

**Focus**: Code quality and compliance with best practices.

**Outcome**: Identifies issues in your code, helping you fix them before running terraform plan or terraform apply.

## Why Use Both?

Using TFLint alongside terraform plan ensures your code is clean and follows best practices, while allowing you to review the specific changes Terraform will make to your infrastructure, thereby minimizing errors and increasing the reliability of your deployments.

## How to Install and Use TFLint:

**Installation**:

Install Go: TFLint is built with Go, so make sure you have Go installed.

You can [download Go from here](https://go.dev/doc/install).

**Install TFLint**:

Run the following command:

```bash
sudo apt  install gccgo-go
go install github.com/terraform-linters/tflint@latest
                       or

curl -s https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh | bash
```

**Verify Installation**:

Check the installed version by running:

```bash
tflint --version

outcomes-
TFLint version 0.52.0
+ ruleset.terraform (0.8.0-bundled)
```

## Using TFLint:

Once installed, using TFLint is straightforward:

Navigate to your Terraform project directory. Run:

```bash
tflint
```

## Install Plugins (if needed):

For AWS, Azure, or GCP, you can install specific plugins to enhance TFLint’s capabilities.

TFLint will analyze your Terraform files and provide a report of any issues or recommendations.

Now, we will configure the plugins by creating a directory called `terraform-tflint/EC2_instance`, but you can use any directory; for my demo, I am using `Terraform-tflint`.

Inside the directory, we will create a file called `.tflint.hcl`. Make sure the file name is the same, and inside this file, we will use the following value:

```bash
plugin "aws" {
    enabled = true
    version = "0.32.0"
    source  = "github.com/terraform-linters/tflint-ruleset-aws"
}

plugin "terraform" {
  enabled = true
  preset  = "recommended"
}
```

Now, we will use `tflint --init` to initialize the plugin.

To Test:

Folder Terraform Structure

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723705672256/475e02ad-f440-4195-8f37-58e7c8b20228.png align="center")

In your [`main.tf`](http://main.tf) file, change the EC2 instance type to an incorrect value like `instance_type = t1.2xlarge`.

```sh
resource "aws_instance" "web" {
  ami           = "ami-0ff8a91507f77f867"
  instance_type = "t1.2xlarge" # invalid type!
}
```

Since `t1.2xlarge` is an invalid instance type, an error will occur when you run `Terraform Apply`, but `Terraform validate` and `Terraform Plan` cannot detect this error in advance because it is specific to the AWS provider and valid in the Terraform language.

## Demo:

```bash
terraform validate
terraform plan
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723705690152/a46ae598-b554-46d1-9e7e-0ba9a0e3bc6a.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723705695742/6dee06d0-3f77-4218-bca6-7b49247d6dff.png align="center")

Now, we will initiate `tflint` and see the result.

```bash
~/terraform-tflint/EC2_instance$ tflint
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723705709893/39a53dfd-5343-48cf-9df1-60a4220026f8.png align="center")

**Note** ⇒ To avoid the warning in the tflint outcome, modify the `.tflint.hcl` file as shown below.

```bash
rule "terraform_required_version" {
  enabled = false
}
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723705724166/8f67a525-f01d-4c43-a15d-4c1f09ae27df.png align="center")

Final view after adding

```bash
plugin "aws" {
    enabled = true
    version = "0.32.0"
    source  = "github.com/terraform-linters/tflint-ruleset-aws"
}

plugin "terraform" {
  enabled = true
  preset  = "recommended"
}

rule "terraform_required_version" {
  enabled = false
}
```

Now, run the command again.

```sh
tflint --init
```

When you run `tflint` again, the warning should not appear..

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1723705747288/e985c553-2bb5-4b23-a77a-55f998e14006.png align="center")

## Conclusion

`TFLint` is an essential tool for anyone working with Terraform. It helps maintain high standards of code quality, reduces the likelihood of errors, and promotes best practices in infrastructure management. By integrating TFLint into your workflow, you can catch issues early, save time, and ensure that your Terraform code is both efficient and secure.

This blog post is designed to provide a comprehensive yet accessible overview of TFLint, along with a clear comparison to the terraform plan. It highlights key points in a way that is easy for readers to grasp, making it suitable for both beginners and more experienced practitioners in the DevOps space.

🚀Ref Link [Installation](https://go.dev/doc/install), [About tflint](https://github.com/terraform-linters/tflint), [tflint-ruleset-aws](https://github.com/terraform-linters/tflint-ruleset-aws)