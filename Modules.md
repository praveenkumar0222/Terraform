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
