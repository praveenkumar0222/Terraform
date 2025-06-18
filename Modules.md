# **Study Material: Building Terraform Modules Locally**  

## **1. Introduction to Terraform Modules**  
**Definition:**  
A **Terraform module** is a reusable set of Terraform configurations that can be called multiple times to create similar infrastructure components.  

**Why Use Modules?**  
- **Reusability:** Avoid writing the same code repeatedly.  
- **Organization:** Keep Terraform configurations clean and manageable.  
- **Collaboration:** Share modules across teams.  

---

## **2. Types of Terraform Modules**  
### **(A) Public Registry Modules**  
- **Definition:** Pre-built modules available in the **Terraform Public Registry** (e.g., AWS VPC, EC2, S3 modules).  
- **Example:**  
  ```hcl
  module "vpc" {
    source  = "terraform-aws-modules/vpc/aws"
    version = "3.14.0"
  }
  ```

### **(B) Local Modules**  
- **Definition:** Modules created and stored **locally** in your project.  
- **When to Use?**  
  - When your infrastructure **does not have internet access** (private networks).  
  - When you need **custom modifications** to existing modules.  

---

## **3. Building Local Modules**  
### **Option 1: Build from Scratch**  
- **When?** If no suitable module exists in the public registry.  
- **Steps:**  
  1. Create a folder (e.g., `modules/aws-vpc`).  
  2. Define:  
     - `main.tf` (main configuration)  
     - `variables.tf` (input variables)  
     - `outputs.tf` (output values)  
     - `versions.tf` (Terraform & provider versions)  

### **Option 2: Modify Existing Public Modules**  
- **When?** If a similar module exists but needs tweaks.  
- **Steps:**  
  1. Download the module from the **Terraform Registry**.  
  2. Copy the code into your `modules` folder.  
  3. Modify as needed.  

---

## **4. Referencing Local Modules**  
Instead of downloading from the registry, reference the **local path**:  
```hcl
module "vpc" {
  source = "./modules/aws-vpc"  # Local module path
}
```

### **Key Differences:**  
| **Public Registry Module** | **Local Module** |  
|----------------------------|------------------|  
| Downloads from `registry.terraform.io` | Stored in your project folder |  
| Requires internet access | Works offline |  
| Managed by HashiCorp/community | Managed by you |  

---

## **5. Best Practices**  
1. **Review Public Modules First**  
   - Before building from scratch, check the **Terraform Registry** for existing modules.  
   - Learn from their structure (`main.tf`, `variables.tf`, etc.).  

2. **Use Dynamic Blocks & Functions**  
   - Example:  
     ```hcl
     dynamic "subnet" {
       for_each = var.subnets
       content {
         cidr_block = subnet.value.cidr
       }
     }
     ```

3. **Test Before Using**  
   - Run `terraform init`, `plan`, and `apply` to verify.  

---

## **6. Example: Creating a Local VPC Module**  
### **Step 1: Folder Structure**  
```
terraform-manifests/  
└── modules/  
    └── aws-vpc/  
        ├── main.tf  
        ├── variables.tf  
        ├── outputs.tf  
        └── versions.tf  
```

### **Step 2: Define `main.tf`**  
```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr
}
```

### **Step 3: Define `variables.tf`**  
```hcl
variable "vpc_cidr" {
  description = "CIDR block for VPC"
  type        = string
  default     = "10.0.0.0/16"
}
```

### **Step 4: Use the Module**  
```hcl
module "my_vpc" {
  source = "./modules/aws-vpc"
  vpc_cidr = "192.168.0.0/16"
}
```

---

## **7. Key Takeaways**  
✅ **Local modules** help when **no internet access** is available.  
✅ You can **build from scratch** or **modify existing modules**.  
✅ Always **review public modules** for best practices.  
✅ Use `source = "./modules/module-name"` to reference locally.  

---
# **Study Material: Building a Reusable Terraform Module for Static Website Hosting on AWS S3**
---

## **2. Steps to Build a Reusable Module**
### **Step 1: Manually Create a Static Website on S3 (V1)**
- **Objective:** Understand the manual steps before automation.
- **Steps:**
  1. **Create an S3 Bucket** (`my-bucket-1045`).
  2. **Enable Static Website Hosting** (Properties → Static website hosting → Enable).
  3. **Disable "Block All Public Access"** (Permissions → Edit → Uncheck).
  4. **Apply a Public Read Policy** (Permissions → Bucket Policy → Paste JSON policy).
  5. **Upload `index.html`** and test access via the S3 website endpoint.

### **Step 2: Automate Using Terraform (V2)**
- **Objective:** Convert manual steps into Terraform code.
- **Files Created:**
  - **`versions.tf`** → Defines Terraform and AWS provider versions.
  - **`variables.tf`** → Defines `bucket_name` and `tags`.
  - **`main.tf`** → Creates S3 bucket with:
    - Public ACL (`acl = "public-read"`).
    - Bucket policy (allows public read access).
    - Static website hosting (`index_document = "index.html"`).
    - Force destroy (`force_destroy = true` for testing).
  - **`outputs.tf`** → Outputs bucket ARN, name, domain, and endpoint.
  - **`terraform.tfvars`** → Sets `bucket_name = "my-bucket-1046"` and `tags`.

- **Execution:**
  ```sh
  terraform init
  terraform validate
  terraform apply -auto-approve
  ```
- **Verification:**  
  - Check if the bucket is created with correct permissions.
  - Upload `index.html` and test the website.

### **Step 3: Convert into a Reusable Module (V3)**
- **Objective:** Make the Terraform code reusable.
- **Module Structure:**
  ```
  modules/
  └── static-website/
      ├── main.tf       # S3 bucket & policy
      ├── outputs.tf    # Bucket ARN, domain, etc.
      ├── variables.tf  # Input variables (bucket_name, tags)
      └── versions.tf   # Terraform & provider versions
  ```
- **How to Use the Module:**
  ```hcl
  module "website" {
    source      = "./modules/static-website"
    bucket_name = "my-bucket-1047"
    tags        = { Environment = "dev" }
  }
  ```

---

## **3. Key Concepts**
### **(A) Public Registry vs. Local Modules**
| **Public Registry Module** | **Local Module** |  
|----------------------------|------------------|  
| Downloads from `registry.terraform.io` | Stored in your project folder |  
| Requires internet access | Works offline |  
| Managed by HashiCorp/community | Managed by you |  

### **(B) Module Components**
1. **`main.tf`** → Core resource definitions.
2. **`variables.tf`** → Input variables (e.g., `bucket_name`).
3. **`outputs.tf`** → Exported values (e.g., `bucket_arn`).
4. **`versions.tf`** → Compatibility constraints.

### **(C) Best Practices**
1. **Review Public Modules First** → Learn from existing modules.
2. **Use Dynamic Blocks & Functions** → For flexible configurations.
3. **Test Before Using** → Run `terraform plan` and `apply`.

---

## **4. Example: Static Website Module**
### **`modules/static-website/main.tf`**
```hcl
resource "aws_s3_bucket" "this" {
  bucket        = var.bucket_name
  acl           = "public-read"
  force_destroy = true

  policy = jsonencode({
    Version = "2012-10-17",
    Statement = [{
      Effect    = "Allow",
      Principal = "*",
      Action    = "s3:GetObject",
      Resource  = "arn:aws:s3:::${var.bucket_name}/*"
    }]
  })

  website {
    index_document = "index.html"
  }

  tags = var.tags
}
```

### **`modules/static-website/variables.tf`**
```hcl
variable "bucket_name" {
  description = "Name of the S3 bucket (must be globally unique)."
  type        = string
}

variable "tags" {
  description = "Tags to apply to the bucket."
  type        = map(string)
  default     = {}
}
```

### **`modules/static-website/outputs.tf`**
```hcl
output "bucket_arn" {
  description = "ARN of the S3 bucket."
  value       = aws_s3_bucket.this.arn
}

output "website_endpoint" {
  description = "S3 static website endpoint."
  value       = aws_s3_bucket.this.website_endpoint
}
```

---

## **5. Key Takeaways**
✅ **Local modules** help when **no internet access** is available.  
✅ You can **build from scratch** or **modify existing modules**.  
✅ Always **review public modules** for best practices.  
✅ Use `source = "./modules/module-name"` to reference locally.  

---

---

## **2. Building a Reusable Module for Static Website Hosting**
### **Step-by-Step Implementation**
#### **Step 1: Manual Setup (V1)**
- Create an S3 bucket manually via AWS Console.
- Enable static website hosting.
- Apply a public read policy.
- Upload `index.html` and test.

#### **Step 2: Automate with Terraform (V2)**
- Convert manual steps into Terraform code:
  - **`main.tf`** → Defines S3 bucket with:
    - Public ACL (`acl = "public-read"`).
    - Bucket policy (allows public read).
    - Static website hosting (`index_document = "index.html"`).
  - **`variables.tf`** → Defines `bucket_name` and `tags`.
  - **`outputs.tf`** → Outputs bucket ARN, domain, etc.

#### **Step 3: Convert to Reusable Module (V3)**
- **Module Structure:**
  ```
  modules/
  └── static-website/
      ├── main.tf       # S3 bucket & policy
      ├── outputs.tf    # Bucket ARN, domain, etc.
      ├── variables.tf  # Input variables (bucket_name, tags)
      └── versions.tf   # Terraform & provider versions
  ```
- **Calling the Module:**
  ```hcl
  module "website" {
    source      = "./modules/static-website"
    bucket_name = "my-bucket-1047"
    tags        = { Environment = "dev" }
  }
  ```

---

## **3. Key Concepts**
### **(A) Public Registry vs. Local Modules**
| **Public Registry Module** | **Local Module** |  
|----------------------------|------------------|  
| Downloaded from `registry.terraform.io` | Stored in your project folder |  
| Requires internet access | Works offline |  
| Managed by HashiCorp/community | Managed by you |  

### **(B) Module Components**
1. **`main.tf`** → Core resource definitions.
2. **`variables.tf`** → Input variables (e.g., `bucket_name`).
3. **`outputs.tf`** → Exported values (e.g., `bucket_arn`).
4. **`versions.tf`** → Compatibility constraints.

### **(C) Best Practices**
1. **Review Public Modules First** → Learn from existing modules.
2. **Use Dynamic Blocks & Functions** → For flexible configurations.
3. **Test Before Using** → Run `terraform plan` and `apply`.

---

## **4. Terraform Get vs. Init**
| **Command** | **Purpose** |  
|-------------|------------|  
| `terraform init` | Initializes backend, downloads providers **and** modules. |  
| `terraform get` | **Only downloads/updates modules** (no backend/provider changes). |  

**When to Use `terraform get`?**  
- When you add a new module but don’t need to reinitialize providers/backend.

---

## **5. Example: Static Website Module**
### **`modules/static-website/main.tf`**
```hcl
resource "aws_s3_bucket" "this" {
  bucket        = var.bucket_name
  acl           = "public-read"
  force_destroy = true

  policy = jsonencode({
    Version = "2012-10-17",
    Statement = [{
      Effect    = "Allow",
      Principal = "*",
      Action    = "s3:GetObject",
      Resource  = "arn:aws:s3:::${var.bucket_name}/*"
    }]
  })

  website {
    index_document = "index.html"
  }

  tags = var.tags
}
```

### **`modules/static-website/variables.tf`**
```hcl
variable "bucket_name" {
  description = "Name of the S3 bucket (must be globally unique)."
  type        = string
}

variable "tags" {
  description = "Tags to apply to the bucket."
  type        = map(string)
  default     = {}
}
```

### **`modules/static-website/outputs.tf`**
```hcl
output "bucket_arn" {
  description = "ARN of the S3 bucket."
  value       = aws_s3_bucket.this.arn
}

output "website_endpoint" {
  description = "S3 static website endpoint."
  value       = aws_s3_bucket.this.website_endpoint
}
```

---

## **6. Key Takeaways**
✅ **Local modules** help when **no internet access** is available.  
✅ You can **build from scratch** or **modify existing modules**.  
✅ Always **review public modules** for best practices.  
✅ Use `source = "./modules/module-name"` to reference locally.  

---
