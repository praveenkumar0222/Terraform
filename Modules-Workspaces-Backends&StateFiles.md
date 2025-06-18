# **Terraform In-Depth Guide: Modules, Workspaces, Backends & State Files**

---

## **Table of Contents**
1. **[Modules](#1-modules)**
   - 1.1 Definition & Purpose
   - 1.2 Types of Modules
   - 1.3 EC2 Instance Module Example
2. **[Workspaces](#2-workspaces)**
   - 2.1 Understanding Workspaces
   - 2.2 EC2 Instance Across Environments (Dev/Stage/Prod)
   - 2.3 Workspace Commands
3. **[Backends](#3-backends)**
   - 3.1 What Are Backends?
   - 3.2 Configuring S3 Backend for EC2 State
4. **[State Files](#4-state-files)**
   - 4.1 Terraform State Explained
   - 4.2 State Locking for EC2 Resources
5. **[Summary & Best Practices](#5-summary--best-practices)**

---

## **1. Modules**

### **1.1 Definition & Purpose**
A **Terraform module** is a container for multiple resources that are used together. Modules help:
- **Reuse** configurations (avoid copy-pasting code)
- **Organize** complex infrastructure
- **Share** infrastructure as code across teams

### **1.2 Types of Modules**
| Type | Description | Example Use Case |
|------|-------------|------------------|
| **Root Module** | Main configuration files in your project | `main.tf`, `variables.tf` |
| **Local Module** | Stored in your project's subdirectory | `modules/ec2-instance` |
| **Public Module** | From Terraform Registry | AWS VPC module |
| **Private Module** | Hosted in private repositories | Company internal modules |

### **1.3 EC2 Instance Module Example**

#### **Module Structure**
```
modules/
└── ec2-instance/
    ├── main.tf          # EC2 resource definition
    ├── variables.tf     # Input variables
    └── outputs.tf       # Output values
```

#### **modules/ec2-instance/main.tf**
```hcl
resource "aws_instance" "this" {
  ami           = var.ami_id
  instance_type = var.instance_type
  tags          = var.tags
}
```

#### **modules/ec2-instance/variables.tf**
```hcl
variable "ami_id" {
  description = "The AMI ID for the EC2 instance"
  type        = string
}

variable "instance_type" {
  description = "Instance type"
  type        = string
  default     = "t3.micro"
}

variable "tags" {
  description = "Tags to apply to the instance"
  type        = map(string)
  default     = {}
}
```

#### **Calling the Module**
```hcl
module "web_server" {
  source        = "./modules/ec2-instance"
  ami_id        = "ami-12345678"
  instance_type = "t3.small"
  tags = {
    Name = "Web Server"
  }
}
```

---

## **2. Workspaces**

### **2.1 Understanding Workspaces**
Workspaces allow you to manage **multiple environments** (dev, stage, prod) using the same configuration but separate state files.

### **2.2 EC2 Instance Across Environments**

#### **Using Workspace in Configuration**
```hcl
resource "aws_instance" "app_server" {
  ami           = "ami-12345678"
  instance_type = terraform.workspace == "prod" ? "t3.large" : "t3.micro"
  
  tags = {
    Name        = "App Server - ${terraform.workspace}"
    Environment = terraform.workspace
  }
}
```

#### **Workspace-Specific Variables**
```hcl
locals {
  instance_settings = {
    dev = {
      instance_type = "t3.micro"
      count         = 1
    }
    prod = {
      instance_type = "t3.large"
      count         = 3
    }
  }
  
  env_settings = local.instance_settings[terraform.workspace]
}

resource "aws_instance" "app_server" {
  count         = local.env_settings.count
  ami           = "ami-12345678"
  instance_type = local.env_settings.instance_type
}
```

### **2.3 Workspace Commands**
```bash
# Create a new workspace
terraform workspace new dev

# List all workspaces
terraform workspace list

# Select a workspace
terraform workspace select prod

# Delete a workspace
terraform workspace delete dev
```

#### **Workspace State Files**
- Each workspace maintains its own state file
- Stored in the backend as:
  ```
  env:/<workspace_name>/terraform.tfstate
  ```

---

## **3. Backends**

### **3.1 What Are Backends?**
Backends determine where Terraform stores its **state file** and how operations are executed.

### **3.2 Configuring S3 Backend for EC2 State**

#### **backend.tf**
```hcl
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "ec2-instances/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}
```

#### **Backend Types Comparison**
| Backend Type | Pros | Cons | Best For |
|-------------|------|------|----------|
| **Local** | Simple, no setup | No locking/versioning | Personal projects |
| **S3** | Versioning, locking | AWS only | AWS environments |
| **Terraform Cloud** | Collaboration features | Paid for teams | Enterprise use |

---

## **4. State Files**

### **4.1 Terraform State Explained**
The state file (`terraform.tfstate`) is a JSON file that:
- Maps resources in your config to real-world resources
- Tracks metadata and dependencies
- Stores resource attributes

#### **Example State Entry for EC2**
```json
{
  "resources": [
    {
      "mode": "managed",
      "type": "aws_instance",
      "name": "app_server",
      "instances": [
        {
          "attributes": {
            "ami": "ami-12345678",
            "instance_type": "t3.micro",
            "id": "i-0abcdef1234567890"
          }
        }
      ]
    }
  ]
}
```

### **4.2 State Locking for EC2 Resources**
Prevents concurrent operations that might corrupt state:

#### **With S3 Backend**
```hcl
resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-locks"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"
  
  attribute {
    name = "LockID"
    type = "S"
  }
}
```

#### **State Operations**
```bash
# List resources in state
terraform state list

# Remove a resource from state
terraform state rm aws_instance.example

# Move a resource (rename in state)
terraform state mv aws_instance.old aws_instance.new
```

---

## **5. Summary & Best Practices**

### **Key Takeaways**
| Concept | Purpose | EC2 Example |
|---------|---------|-------------|
| **Modules** | Reusable components | EC2 instance module with variables |
| **Workspaces** | Environment isolation | Different instance sizes per env |
| **Backends** | State storage | S3 bucket for team access |
| **State** | Resource tracking | EC2 instance metadata |

### **Best Practices**
1. **Modules**
   - Start with small modules
   - Use version constraints for public modules
   ```hcl
   module "vpc" {
     source  = "terraform-aws-modules/vpc/aws"
     version = "3.14.0"
   }
   ```

2. **Workspaces**
   - Use for environment separation (not regions)
   - Combine with input variables for flexibility

3. **Backends**
   - Always use remote backends for teams
   - Enable state locking and encryption

4. **State**
   - Never edit manually
   - Use `terraform state` commands for modifications
   - Store sensitive outputs securely

### **Further Reading**
- [Terraform Modules Documentation](https://www.terraform.io/language/modules)
- [Workspaces Best Practices](https://developer.hashicorp.com/terraform/cli/workspaces)
- [State Management](https://developer.hashicorp.com/terraform/language/state)

---

## **Hands-On Exercise**
1. Create an EC2 module with variables for AMI and instance type
2. Deploy to dev/prod workspaces with different sizes
3. Configure an S3 backend with state locking
4. Inspect the state file after deployment

> **Tip**: Use `terraform plan` before each apply to see changes!

---

This guide follows standard technical documentation format with:
- Clear section hierarchy
- Practical examples
- Comparison tables
- Command references
- Best practice callouts
