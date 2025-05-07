### **Study Material: Terraform Zero to Hero Series - Day 1**

---

### **Key Topics Covered in Day 1**
1. **What is Infrastructure as Code (IaC)?**
2. **Why Terraform?**
3. **Installation of Terraform** (Mac, Linux, Windows)
4. **Setting up Terraform for AWS**
5. **Writing Your First Terraform Code** (Launching an EC2 Instance)
6. **Terraform Lifecycle**
7. **Basics of Terraform State File**

---

### **1. What is Infrastructure as Code (IaC)?**

#### **Definition**
Infrastructure as Code (IaC) is the process of managing and provisioning infrastructure through code instead of manual processes. It allows you to automate the deployment, configuration, and management of infrastructure using scripts or templates.

#### **Why IaC?**
- **Manual Process**: Time-consuming and error-prone.
- **Automation**: Reduces effort, ensures consistency, and speeds up deployments.

#### **Example**
- **Manual Process**: Creating an S3 bucket manually via AWS Console.
- **Automated Process**: Using a script (Python, AWS CLI, or Terraform) to create an S3 bucket programmatically.

#### **Tools for IaC**
- **AWS**: CloudFormation Templates (CFT)
- **Azure**: Azure Resource Manager (ARM)
- **OpenStack**: Heat Templates
- **Terraform**: Universal IaC tool (supports multiple cloud providers)

---

### **2. Why Terraform?**

#### **Universal Approach**
- Terraform supports multiple cloud providers (AWS, Azure, GCP, OpenStack, etc.).
- You only need to learn Terraform’s HashiCorp Configuration Language (HCL) to automate infrastructure across different platforms.

#### **Advantages**
- **Single Tool**: No need to learn multiple IaC tools (e.g., CFT, ARM, Heat Templates).
- **Community Support**: Large community and extensive documentation.
- **API as Code**: Terraform converts HCL code into API calls for the respective cloud provider.

#### **Competitors**
- **Pulumi**, **Crossplane**: Emerging alternatives, but Terraform remains the most widely used.

---

### **3. Installation of Terraform**

#### **Steps to Install Terraform**
1. **Download Terraform**:
   - Visit the [Terraform Downloads Page](https://www.terraform.io/downloads.html).
   - Select the appropriate version for your OS (Windows, Linux, Mac).

2. **Install Terraform**:
   - **Mac/Linux**: Use the terminal to run the installation commands.
   - **Windows**: Download the executable and add it to your system PATH.

3. **Verify Installation**:
   - Run `terraform --version` in the terminal to check if Terraform is installed.

#### **Alternative: GitHub CodeSpaces**
- If you don’t have a local machine, use **GitHub CodeSpaces** (free for 60 hours/month).
- Steps:
  1. Fork the Terraform Zero to Hero repository.
  2. Create a CodeSpace.
  3. Install Terraform and AWS CLI in the CodeSpace environment.

---

### **4. Setting Up Terraform for AWS**

#### **Steps**
1. **Create AWS Access Keys**:
   - Log in to AWS Console.
   - Go to IAM > Security Credentials > Create Access Key.
   - Save the Access Key ID and Secret Access Key.

2. **Configure AWS CLI**:
   - Run `aws configure` in the terminal.
   - Provide the Access Key, Secret Key, Region, and Output Format.

3. **Verify AWS CLI**:
   - Run `aws s3 ls` to list S3 buckets and ensure authentication works.

---

### **5. Writing Your First Terraform Code**

#### **Terraform File Structure**
- **main.tf**: Main configuration file.
- **provider.tf**: Specifies the cloud provider (e.g., AWS, Azure).
- **resource.tf**: Defines the resources to be created (e.g., EC2 instance, S3 bucket).

#### **Example: Launching an EC2 Instance**
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0" # Replace with your AMI ID
  instance_type = "t2.micro"
  subnet_id     = "subnet-0bb1c79de3EXAMPLE" # Replace with your subnet ID
  key_name      = "aws-login" # Replace with your key pair name
}
```

#### **Terraform Commands**
1. **terraform init**: Initializes the working directory and downloads provider plugins.
2. **terraform plan**: Shows what Terraform will do (dry run).
3. **terraform apply**: Applies the configuration and creates resources.
4. **terraform destroy**: Destroys the resources created by Terraform.

---

### **6. Terraform Lifecycle**

#### **Phases**
1. **Init**: Initializes the configuration and downloads plugins.
2. **Plan**: Shows the execution plan (what Terraform will do).
3. **Apply**: Creates or updates resources.
4. **Destroy**: Deletes resources.

---

### **7. Basics of Terraform State File**

#### **What is a State File?**
- **terraform.tfstate**: A JSON file that stores the state of the infrastructure managed by Terraform.
- **Purpose**: Tracks the resources created by Terraform and their current state.

#### **Example**
```json
{
  "version": 4,
  "terraform_version": "1.0.0",
  "resources": [
    {
      "type": "aws_instance",
      "name": "example",
      "instances": [
        {
          "attributes": {
            "ami": "ami-0c55b159cbfafe1f0",
            "instance_type": "t2.micro",
            "id": "i-0abcd1234efgh5678"
          }
        }
      ]
    }
  ]
}
```

#### **Importance**
- **Tracking**: Keeps track of resources.
- **Sensitive Data**: Avoid storing sensitive data in the state file (e.g., passwords, keys).

---

### **Summary of Day 1**
- **Infrastructure as Code (IaC)**: Automating infrastructure using code.
- **Terraform**: A universal IaC tool for multiple cloud providers.
- **Installation**: Easy installation on Mac, Linux, Windows, or using GitHub CodeSpaces.
- **AWS Setup**: Configuring Terraform for AWS using AWS CLI.
- **First Terraform Code**: Writing and applying a Terraform script to launch an EC2 instance.
- **Terraform Lifecycle**: Init, Plan, Apply, Destroy.
- **State File**: Tracks the state of resources managed by Terraform.

---

### **Next Steps**
- **Day 2**: Dive deeper into Terraform configurations, variables, and modules.
- **Practice**: Try creating more resources (e.g., S3 bucket, VPC) using Terraform.

---

### **Additional Resources**
- **GitHub Repository**: [Terraform Zero to Hero](https://github.com/iamveeramalla/terraform-zero-to-hero)
- **Terraform Documentation**: [Terraform Docs](https://www.terraform.io/docs)
- **AWS CLI Documentation**: [AWS CLI Docs](https://aws.amazon.com/cli/)

---

### **Visuals and Tables**

#### **Terraform Lifecycle Diagram**
```
Init → Plan → Apply → Destroy
```

#### **Terraform State File Example**
| Key           | Value                        |
|---------------|------------------------------|
| Resource Type | aws_instance                 |
| Resource Name | example                      |
| AMI           | ami-0c55b159cbfafe1f0        |
| Instance Type | t2.micro                     |
| Instance ID   | i-0abcd1234efgh5678          |
```

---

This study material provides a comprehensive overview of Day 1 of the Terraform Zero to Hero series. Use the GitHub repository and practice the examples to solidify your understanding. Happy learning! 🚀



Terraform Zero to Hero Series - Day 1
Introduction
•	Instructor: Abhishek Veeramalla
•	Series Name: Terraform Zero to Hero
•	Duration: 7 Days
•	Prerequisites: None
•	Objective: Learn Terraform from scratch and automate infrastructure provisioning.
________________________________________
Key Topics Covered
1.	Infrastructure as Code (IaC)
2.	Why Terraform?
3.	Installation of Terraform (Mac, Linux, Windows)
4.	Setting up Terraform for AWS
5.	Writing First Terraform Code (Launching an EC2 Instance)
6.	Terraform Lifecycle
7.	Basics of Terraform State File
________________________________________
1. Infrastructure as Code (IaC)
Definition
Infrastructure as Code (IaC) is managing and provisioning infrastructure through code instead of manual processes.
Benefits
•	Automation: Reduces effort and speeds up deployments.
•	Consistency: Ensures repeatable and error-free infrastructure setups.
•	Version Control: Allows tracking and reverting changes.
Popular IaC Tools
Platform	Tool
AWS	CloudFormation
Azure	ARM Templates
OpenStack	Heat Templates
Multi-Cloud	Terraform
________________________________________
2. Why Terraform?
Key Features
•	Multi-Cloud Support: AWS, Azure, GCP, etc.
•	Declarative Syntax: Uses HCL (HashiCorp Configuration Language).
•	Immutable Infrastructure: Ensures consistency between environments.
•	Strong Community Support.
Terraform vs. Competitors
Feature	Terraform	CloudFormation	Pulumi
Multi-Cloud	✅	❌	✅
Open Source	✅	❌	✅
State Management	✅	✅	✅
________________________________________
3. Installation of Terraform
Steps to Install Terraform
1.	Download Terraform from Terraform Downloads Page.
2.	Install Terraform 
o	Mac/Linux: Extract and move the binary to /usr/local/bin/.
o	Windows: Add Terraform binary to system PATH.
3.	Verify Installation: 
4.	terraform --version
Alternative: GitHub CodeSpaces
•	Use GitHub CodeSpaces for a cloud-based development environment.
•	Install Terraform and AWS CLI within CodeSpaces.
________________________________________
4. Setting Up Terraform for AWS
AWS Configuration
1.	Create AWS Access Keys 
o	AWS Console → IAM → Security Credentials → Create Access Key.
2.	Configure AWS CLI 
3.	aws configure
4.	Verify AWS CLI 
5.	aws s3 ls
________________________________________
5. Writing Your First Terraform Code
Terraform File Structure
File Name	Purpose
main.tf	Main configuration file
provider.tf	Specifies cloud provider
resource.tf	Defines resources
Example: Launching an EC2 Instance
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
Terraform Commands
terraform init     # Initialize Terraform
terraform plan     # Show execution plan
terraform apply    # Apply changes
terraform destroy  # Destroy resources
________________________________________
6. Terraform Lifecycle
Phases
1.	Init: Downloads providers and initializes Terraform.
2.	Plan: Shows planned changes.
3.	Apply: Creates or updates resources.
4.	Destroy: Removes infrastructure.
________________________________________
7. Basics of Terraform State File
What is a State File?
•	Stores metadata about managed resources.
•	Tracks real-world infrastructure.
Example terraform.tfstate Content
{
  "resources": [
    {
      "type": "aws_instance",
      "name": "example",
      "instances": [
        {
          "attributes": {
            "ami": "ami-0c55b159cbfafe1f0",
            "instance_type": "t2.micro",
            "id": "i-0abcd1234efgh5678"
          }
        }
      ]
    }
  ]
}
Best Practices
•	Avoid storing sensitive data in state files.
•	Use remote state storage (e.g., AWS S3) for collaboration.
________________________________________
Summary of Day 1
Topic	Key Takeaways
IaC	Automates infrastructure with code
Terraform	Multi-cloud IaC tool with HCL syntax
Installation	Install Terraform on Mac, Linux, Windows
AWS Setup	Configure AWS CLI and access keys
First Code	Launch an EC2 instance using Terraform
Lifecycle	Init, Plan, Apply, Destroy
State File	Tracks infrastructure state
________________________________________
Next Steps
•	Day 2: Terraform variables, modules, and configurations.
•	Practice: Deploy an S3 bucket using Terraform.
________________________________________
Additional Resources
•	Terraform Documentation
•	AWS CLI Documentation
•	GitHub Repository
________________________________________
This document provides a structured summary of Terraform Zero to Hero - Day 1. Use the resources and examples to solidify your understanding. Happy learning! 🚀

