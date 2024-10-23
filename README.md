# Learning Terraform

- [Learning Terraform](#learning-terraform)
  - [Downloading and Accessing Terraform](#downloading-and-accessing-terraform)
    - [Download Terraform on Windows](#download-terraform-on-windows)
    - [Adding Terraform Path to System Environment Variables](#adding-terraform-path-to-system-environment-variables)
  - [Terraform Research](#terraform-research)
    - [What is Terraform? What is it used for?](#what-is-terraform-what-is-it-used-for)
    - [Why use Terraform? The benefits?](#why-use-terraform-the-benefits)
    - [Alternatives to Terraform](#alternatives-to-terraform)
    - [Who is using Terraform in the industry?](#who-is-using-terraform-in-the-industry)
    - [In IaC, what is orchestration? How does Terraform act as "orchestrator"?](#in-iac-what-is-orchestration-how-does-terraform-act-as-orchestrator)
    - [Best practice supplying AWS credentials to Terraform](#best-practice-supplying-aws-credentials-to-terraform)
    - [Terraform AWS credentials lookup order (precedence)](#terraform-aws-credentials-lookup-order-precedence)
    - [Best practice to supply AWS credentials](#best-practice-to-supply-aws-credentials)
    - [How should AWS credentials never be passed to Terraform?](#how-should-aws-credentials-never-be-passed-to-terraform)
    - [Why use Terraform for different environments (e.g. production, testing, etc.)?](#why-use-terraform-for-different-environments-eg-production-testing-etc)
    - [What is pull and push configuration management (IaC)?](#what-is-pull-and-push-configuration-management-iac)
    - [Which tools support push/pull?](#which-tools-support-pushpull)
    - [Does Terraform use the push or pull configuration?](#does-terraform-use-the-push-or-pull-configuration)
    - [Which is better: push or pull configuration management?](#which-is-better-push-or-pull-configuration-management)
  - [Terraform Implementation](#terraform-implementation)
    - [Securing Sensitive Files/Credentials](#securing-sensitive-filescredentials)
    - [Creating an EC2 Instance via Terraform](#creating-an-ec2-instance-via-terraform)
    - [Running Terraform Commands](#running-terraform-commands)
    - [Creating NSG Rules via Terraform](#creating-nsg-rules-via-terraform)
    - [Creating an EC2 Instance and attaching NSG rules via Terraform](#creating-an-ec2-instance-and-attaching-nsg-rules-via-terraform)

## Downloading and Accessing Terraform

### Download Terraform on Windows

1. Browse to the Download Terraform page https://developer.hashicorp.com/terraform/install.
2. Look for the Windows download link.<br>
![Terraform download link](./images/terraform-link-windows.png)<br>
3. Unzip the downloaded file. For example, use the C:\Hashicorp\Terraform path. Remember this location so you can add the path to the environment variables.

### Adding Terraform Path to System Environment Variables

1. Open the Windows start menu, start typing environment and click **Edit system environment variables**. The System Properties window opens.<br>![Searching for environment variable in Windows start menu](./images/environment-variable-setup.png)<br>
2. Click the Environment Variables.<br>![System Properties](./images/system-properties.png)<br>
3. Select the **Path** variable in the **System variables** section to add terraform for all accounts. Alternatively, select **Path** in the **User variables** section to add terraform for the currently logged-in user only. Click **Edit** once you select a Path.<br>![Path Variable](./images/path-variable.png)<br>
4. Click **New** in the edit window and enter the location of the Terraform folder (eg. C:\Hashicorp\Terraform).<br>![Setting environment variable for Terraform](./images/setting-env-path.png)<br>
5. Click **OK** on all windows to apply the changes.
6. Verify installation by opening a new commant-prompt window and checking the Terraform version.

```bash
terraform -version
```

## Terraform Research

### What is Terraform? What is it used for?

Terraform is an open-source Infrastructure as Code (IaC) tool developed by HashiCorp. It allows users to define, manage, and provision infrastructure using a declarative configuration language (HCL). Terraform is used to automate the creation, modification, and destruction of cloud resources across various providers like AWS, Azure, and GCP.

* Originally inspired by CloudFormation.
* Immutable

### Why use Terraform? The benefits?

* Multi-cloud support: Manages infrastructure across different cloud providers.
* Declarative syntax: Infrastructure is defined in simple, human-readable code.
* Version control: Infrastructure changes can be tracked in version control systems.
* Scalability: Automates infrastructure at any scale.
* State management: Keeps a record of the current infrastructure state.
* Reusable modules: Infrastructure can be modularized and reused.
* Cloud Agnostic: Deploy to any cloud platform. Each Cloud provider provides plugins to seemlessly integrate with their API interface.
* Open-source
* Easy-to-use

### Alternatives to Terraform

* AWS CloudFormation
* Pulumi
* Ansible
* Chef
* SaltStack
* Google Cloud Deployment Manager

### Who is using Terraform in the industry?

Companies like Netflix, Uber, Airbnb, Expedia, and Samsung are known to use Terraform for managing their infrastructure.

### In IaC, what is orchestration? How does Terraform act as "orchestrator"?

Orchestration refers to automating and managing multiple interconnected resources or services. Terraform acts as an orchestrator by managing the dependencies between resources and executing them in the correct order, ensuring that infrastructure is provisioned or updated properly.

### Best practice supplying AWS credentials to Terraform

* IAM roles: For services like EC2 or ECS, use IAM roles instead of passing credentials directly. This is more secure and avoids exposing sensitive information.
* Environment variables: Set AWS credentials in your environment using variables like AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY.
* Credentials file: Use the AWS credentials file (~/.aws/credentials) to store credentials and reference profiles.
* AWS SSO: Leverage AWS Single Sign-On for secure access without embedding credentials in your infrastructure code.

### Terraform AWS credentials lookup order (precedence)

1. Environment variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)
2. AWS credentials file (~/.aws/credentials)
3. AWS config file (~/.aws/config)
4. EC2 instance profile or ECS container credentials
5. AWS Vault, AWS SSO

### Best practice to supply AWS credentials

Use IAM roles or AWS SSO for secure, managed access.

### How should AWS credentials never be passed to Terraform?

AWS credentials should never be hard-coded directly in Terraform configuration files or embedded within the source code. Always avoid exposing sensitive information in plaintext within your codebase.

### Why use Terraform for different environments (e.g. production, testing, etc.)?

Terraform allows for consistent infrastructure deployment across environments (production, testing, staging). By using separate workspaces or configuration files, teams can ensure infrastructure consistency while managing environment-specific variations efficiently.

* Developers using different testing environments will cause issues when bringing new implementations together.
* Production is scalable.
* Spin up infrastructure, mirrors production and tear them down.

### What is pull and push configuration management (IaC)?

* **Push Configuration Management**: A central server pushes configuration changes to target systems. The server knows the desired state and actively applies the updates.

* **Pull Configuration Management**: Target systems pull configuration updates from a central server or repository, regularly checking for updates to enforce the desired state.

### Which tools support push/pull?

* **Push Tools**: Ansible, SaltStack (master mode).
* **Pull Tools**: Puppet, Chef, SaltStack (agent mode).

### Does Terraform use the push or pull configuration?

Terraform uses a push model. The user triggers changes (via terraform apply), and Terraform pushes updates to match the desired state defined in the code. It doesn’t use continuous pulling like Puppet.

### Which is better: push or pull configuration management?

* Push Model is better for direct control and real-time feedback, ideal for small to medium systems and immediate updates.
* Pull Model is better for scalability and long-term consistency, ideal for large distributed systems that need regular updates.<br>
*The choice depends on the need for centralized control (push) versus decentralized, periodic updates (pull).*

## Terraform Implementation

### Securing Sensitive Files/Credentials

*When setting up Terraform, it is crucial to protect sensitive files, especially those containing credentials. To secure these files, you should use a .gitignore file to prevent them from being pushed to version control.*

1. Initialize Git Repository: Run `git init` in your project directory to initialize version control.
2. Create a `.gitignore` File:

*This file will specify the files and directories to be ignored by Git, helping to secure sensitive data and prevent unnecessary files from bloating the repository.*

```bash
# .terraform.lock.hcl - Plugin version lock file (No sensitive information)
# This file locks the provider version (e.g., AWS provider version). It does not contain sensitive information, so it's not required to be ignored.
.terraform.lock.hcl

# .terraform/ - Terraform working directory (No sensitive information)
# Contains temporary files and caches. Ignored to avoid unnecessary bloat in the repository.
.terraform/

# terraform.tfstate and terraform.tfstate.backup - Sensitive files
# These files store information about the infrastructure's current state, which often includes credentials, IP addresses, and other sensitive data.
terraform.tfstate
terraform.tfstate.backup

# Variables files - Can contain sensitive data
# Terraform variables are often used to store sensitive data (e.g., AWS access keys, passwords).
*.tfvars
*.auto.tfvars
variable.tf

# Override files - Used for overriding configurations
# These files can be ignored as they are often used for custom configurations that should not be tracked.
override.tf
override.tf.json
```

***Never store AWS access keys, secret keys, or any sensitive information directly in the Terraform configuration files.***

### Creating an EC2 Instance via Terraform

1. Define the AWS Provider: Terraform requires a provider block to define which cloud provider (in this case, AWS) and region the infrastructure will be deployed in.

```bash
provider "aws" {
  # Specify the AWS region for resource deployment
  region = "eu-west-1"
}
```
2. Create the EC2 Instance Resource: Use the aws_instance resource to define an EC2 instance. The resource block specifies the AMI ID, instance type, and other configuration details.

```bash
resource "aws_instance" "app_instance" {
  # Specify the Amazon Machine Image (AMI) to use
  ami = "ami-0c1c30571d2dae5c9"  # Ubuntu 22.04 LTS AMI for eu-west-1

  # Specify the instance type (e.g., t2.micro for a free tier instance)
  instance_type = "t2.micro"

  # Ensure the instance is assigned a public IP address
  associate_public_ip_address = true

  # Add tags for better resource management (e.g., name, environment)
  tags = {
    Name = "tech264-adonis-tf-app-instance"
  }
}
```

### Running Terraform Commands

*After writing the Terraform configuration, you can execute the following commands to manage your infrastructure.*

1. Initialize the Terraform Workspace: This command initializes the working directory by downloading necessary provider plugins and preparing the backend for managing the infrastructure state.

```bash
terraform init
```

2. Plan the Infrastructure Changes: This previews the changes that Terraform will make to your infrastructure based on the configuration. It checks for errors and shows a detailed execution plan without making any changes.

```bash
terraform plan
```

3. Apply the Changes: This command applies the changes outlined in the plan, creating or updating the resources as defined in the configuration.

```bash
terraform apply
```

4. Destroy the Infrastructure: This command deletes all the resources defined in the Terraform configuration, cleaning up the infrastructure.

```bash
terraform destroy
```

### Creating NSG Rules via Terraform

```bash
provider "aws" {
  region = "eu-west-1"
}

resource "aws_security_group" "tech264_adonis_tf_allow_port_22_3000_80" {

  # Allow port 22 (SSH) from localhost (127.0.0.1)
  ingress {
    description      = "Allow SSH from localhost"
    from_port        = 22
    to_port          = 22
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  # Allow port 3000 from all (0.0.0.0/0)
  ingress {
    description      = "Allow access to port 3000 from all"
    from_port        = 3000
    to_port          = 3000
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  # Allow port 80 (HTTP) from all (0.0.0.0/0)
  ingress {
    description      = "Allow access to port 80 from all"
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  tags = {
    Name = "tech264-adonis-tf-allow-port-22-3000-80"
  }
}
```

### Creating an EC2 Instance and attaching NSG rules via Terraform

1. Update EC2 Configuration in main.tf: Ensure your EC2 instance resource block includes:
   * `key_name`: This variable references the SSH key to establish SSH connections to the EC2 instance.
   * `vpc_security_group_ids`: Requires a list of security group id's already created in AWS

```bash
provider "aws" {
  # Region to create infrastructure
  region = "eu-west-1"
}

# Define the AWS instance and attach the existing Security Group and Key Pair
resource "aws_instance" "app_instance" {
  # Specify the AMI ID ami-0c1c30571d2dae5c9 (for Ubuntu 22.04 LTS)
  ami = "ami-0c1c30571d2dae5c9"

  # Specify the instance type - t2.micro
  instance_type = "t2.micro"

  # Add a Public IP address to this instance
  associate_public_ip_address = true

  # Attach the existing security group using the ID
  vpc_security_group_ids = ["sg-021d49f73d3a549ad"]  # Replace with your actual security group ID

  # Attach the key pair to enable SSH access
  key_name = "tech264-adonis-aws-key"

  # Tag the instance with a name
  tags = {
    Name = "tech264-adonis-tf-app-instance"
  }
}
```