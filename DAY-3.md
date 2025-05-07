### **Study Material: Terraform Zero to Hero Series - Day 3**

---

### **Recap of Day 2**
1. **Providers**: Configured multi-region and hybrid cloud setups.
2. **Resources**: Learned how to define and reference resources.
3. **Variables**: Used input and output variables to parameterize configurations.
4. **TFVars**: Passed variable values using `terraform.tfvars`.
5. **Conditional Expressions**: Added logic to Terraform configurations.
6. **Built-in Functions**: Used functions like `length` and `map`.

---

### **Key Topics Covered in Day 3**
1. **What are Terraform Modules?**
2. **Advantages of Using Modules**
3. **Creating a Terraform Module**
4. **Using a Terraform Module**
5. **Publicly Available Modules (Terraform Registry)**

---

### **1. What are Terraform Modules?**

#### **Definition**
- A **module** in Terraform is a container for multiple resources that are used together. It allows you to group resources into reusable components.
- Modules help in organizing and reusing Terraform code, similar to functions in programming.

#### **Why Use Modules?**
- **Reusability**: Write once, use multiple times.
- **Maintainability**: Easier to manage and update.
- **Collaboration**: Teams can work on different modules independently.
- **Abstraction**: Hide complexity by exposing only necessary inputs and outputs.

---

### **2. Advantages of Using Modules**

#### **1. Modularity**
- Break down complex infrastructure into smaller, manageable components.

#### **2. Reusability**
- Use the same module across multiple projects or teams.

#### **3. Simplified Collaboration**
- Teams can work on different modules without stepping on each other’s toes.

#### **4. Versioning**
- Modules can be versioned, allowing you to track changes and roll back if needed.

#### **5. Testing**
- Easier to test individual modules in isolation.

#### **6. Documentation**
- Modules can be documented, making it easier for others to understand and use them.

---

### **3. Creating a Terraform Module**

#### **Steps to Create a Module**
1. **Create a Folder**: Create a folder for your module (e.g., `modules/ec2_instance`).
2. **Move Files**: Move the Terraform files (`main.tf`, `variables.tf`, `outputs.tf`) into the module folder.
3. **Define Inputs and Outputs**: Ensure the module has clear inputs (variables) and outputs.

#### **Example: EC2 Instance Module**
- **Folder Structure**:
  ```
  modules/
  └── ec2_instance/
      ├── main.tf
      ├── variables.tf
      └── outputs.tf
  ```

- **main.tf**:
  ```hcl
  resource "aws_instance" "example" {
    ami           = var.ami_value
    instance_type = var.instance_type_value
    subnet_id     = var.subnet_id_value
  }
  ```

- **variables.tf**:
  ```hcl
  variable "ami_value" {
    description = "Value for the AMI"
  }

  variable "instance_type_value" {
    description = "Value for the instance type"
  }

  variable "subnet_id_value" {
    description = "Value for the subnet ID"
  }
  ```

- **outputs.tf**:
  ```hcl
  output "public_ip" {
    value = aws_instance.example.public_ip
  }
  ```

---

### **4. Using a Terraform Module**

#### **Steps to Use a Module**
1. **Reference the Module**: In your main Terraform configuration, reference the module using the `module` block.
2. **Pass Inputs**: Provide values for the module’s input variables.
3. **Use Outputs**: Use the module’s outputs in your main configuration.

#### **Example: Using the EC2 Instance Module**
- **main.tf**:
  ```hcl
  provider "aws" {
    region = "us-east-1"
  }

  module "ec2_instance" {
    source = "./modules/ec2_instance"

    ami_value           = "ami-0c55b159cbfafe1f0"
    instance_type_value = "t2.micro"
    subnet_id_value     = "subnet-0bb1c79de3EXAMPLE"
  }

  output "public_ip" {
    value = module.ec2_instance.public_ip
  }
  ```

- **terraform.tfvars**:
  ```hcl
  ami_value           = "ami-0c55b159cbfafe1f0"
  instance_type_value = "t2.micro"
  subnet_id_value     = "subnet-0bb1c79de3EXAMPLE"
  ```

---

### **5. Publicly Available Modules (Terraform Registry)**

#### **What is Terraform Registry?**
- The **Terraform Registry** is a repository of publicly available modules that you can use in your Terraform configurations.
- Similar to Docker Hub, it allows you to reuse modules created by others.

#### **Example: Using a Public Module**
- **main.tf**:
  ```hcl
  module "ec2_instance" {
    source  = "terraform-aws-modules/ec2-instance/aws"
    version = "3.0.0"

    ami           = "ami-0c55b159cbfafe1f0"
    instance_type = "t2.micro"
    subnet_id     = "subnet-0bb1c79de3EXAMPLE"
  }
  ```

#### **Advantages of Public Modules**
- **Pre-built**: Saves time by using pre-built modules.
- **Community Support**: Modules are often maintained by the community or cloud providers.
- **Documentation**: Public modules come with documentation, making them easy to use.

#### **Disadvantages of Public Modules**
- **Security**: You may not know who maintains the module.
- **Customization**: Limited flexibility compared to writing your own modules.

---

### **Summary of Day 3**
1. **Modules**: Learned how to create and use Terraform modules.
2. **Advantages**: Understood the benefits of modularizing Terraform code.
3. **Public Modules**: Explored the Terraform Registry and how to use public modules.

---

### **Next Steps**
- **Day 4**: Deep dive into Terraform state management, remote backends, and state locking.
- **Practice**: Try creating your own modules and using them in different projects.

---

### **Additional Resources**
- **GitHub Repository**: [Terraform Zero to Hero](https://github.com/iamveeramalla/terraform-zero-to-hero)
- **Terraform Documentation**: [Terraform Docs](https://www.terraform.io/docs)
- **Terraform Registry**: [Terraform Registry](https://registry.terraform.io/)

---

### **Visuals and Tables**

#### **Terraform Module Structure**
```
modules/
└── ec2_instance/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

#### **Using a Module**
```hcl
module "ec2_instance" {
  source = "./modules/ec2_instance"

  ami_value           = "ami-0c55b159cbfafe1f0"
  instance_type_value = "t2.micro"
  subnet_id_value     = "subnet-0bb1c79de3EXAMPLE"
}
```

---

This study material provides a comprehensive overview of Day 3 of the Terraform Zero to Hero series. Use the GitHub repository and practice the examples to solidify your understanding. Happy learning! 🚀


Study Material: Terraform Zero to Hero Series - Day 3
Introduction
•	Instructor: Abhishek Veeramalla
•	Series Name: Terraform Zero to Hero
•	Duration: 7 Days
•	Objective: Deep dive into Terraform modules, understand their importance, and learn how to create and use modules in Terraform.
Recap of Day 2
1.	Providers: Configured multi-region and hybrid cloud setups.
2.	Resources: Learned how to define and reference resources.
3.	Variables: Used input and output variables to parameterize configurations.
4.	TFVars: Passed variable values using terraform.tfvars.
5.	Conditional Expressions: Added logic to Terraform configurations.
6.	Built-in Functions: Used functions like length and map.
________________________________________
Key Topics Covered in Day 3
1.	What are Terraform Modules?
2.	Advantages of Using Modules
3.	Creating a Terraform Module
4.	Using a Terraform Module
5.	Publicly Available Modules (Terraform Registry)
1. What are Terraform Modules?
Definition
•	A module in Terraform is a container for multiple resources that are used together. It allows you to group resources into reusable components.
•	Modules help in organizing and reusing Terraform code, similar to functions in programming.
Why Use Modules?
•	Reusability: Write once, use multiple times.
•	Maintainability: Easier to manage and update.
•	Collaboration: Teams can work on different modules independently.
•	Abstraction: Hide complexity by exposing only necessary inputs and outputs.
________________________________________
2. Advantages of Using Modules
Advantage	Description
Modularity	Break down complex infrastructure into smaller, manageable components.
Reusability	Use the same module across multiple projects or teams.
Collaboration	Teams can work on different modules without conflicts.
Versioning	Track changes and roll back if needed.
Testing	Easier to test individual modules in isolation.
Documentation	Makes it easier for others to understand and use them.
________________________________________
3. Creating a Terraform Module
Steps to Create a Module
1.	Create a Folder: Create a folder for your module (e.g., modules/ec2_instance).
2.	Move Files: Move Terraform files (main.tf, variables.tf, outputs.tf) into the module folder.
3.	Define Inputs and Outputs: Ensure the module has clear inputs (variables) and outputs.
Example: EC2 Instance Module
Folder Structure:
modules/
└── ec2_instance/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
main.tf:
resource "aws_instance" "example" {
  ami           = var.ami_value
  instance_type = var.instance_type_value
  subnet_id     = var.subnet_id_value
}
variables.tf:
variable "ami_value" {
  description = "Value for the AMI"
}

variable "instance_type_value" {
  description = "Value for the instance type"
}

variable "subnet_id_value" {
  description = "Value for the subnet ID"
}
outputs.tf:
output "public_ip" {
  value = aws_instance.example.public_ip
}
________________________________________
4. Using a Terraform Module
Steps to Use a Module
1.	Reference the Module: In your main Terraform configuration, reference the module using the module block.
2.	Pass Inputs: Provide values for the module’s input variables.
3.	Use Outputs: Use the module’s outputs in your main configuration.
Example: Using the EC2 Instance Module
main.tf:
provider "aws" {
  region = "us-east-1"
}

module "ec2_instance" {
  source = "./modules/ec2_instance"
  
  ami_value           = "ami-0c55b159cbfafe1f0"
  instance_type_value = "t2.micro"
  subnet_id_value     = "subnet-0bb1c79de3EXAMPLE"
}

output "public_ip" {
  value = module.ec2_instance.public_ip
}
terraform.tfvars:
ami_value           = "ami-0c55b159cbfafe1f0"
instance_type_value = "t2.micro"
subnet_id_value     = "subnet-0bb1c79de3EXAMPLE"
________________________________________
5. Publicly Available Modules (Terraform Registry)
What is Terraform Registry?
•	The Terraform Registry is a repository of publicly available modules that you can use in your Terraform configurations.
•	Similar to Docker Hub, it allows you to reuse modules created by others.
Example: Using a Public Module
main.tf:
module "ec2_instance" {
  source  = "terraform-aws-modules/ec2-instance/aws"
  version = "3.0.0"

  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  subnet_id     = "subnet-0bb1c79de3EXAMPLE"
}
Advantages of Public Modules
Advantage	Description
Pre-built	Saves time by using pre-built modules.
Community Support	Modules are maintained by the community or cloud providers.
Documentation	Public modules come with documentation, making them easy to use.
Disadvantages of Public Modules
Disadvantage	Description
Security	You may not know who maintains the module.
Customization	Limited flexibility compared to writing your own modules.
________________________________________
Summary of Day 3
1.	Modules: Learned how to create and use Terraform modules.
2.	Advantages: Understood the benefits of modularizing Terraform code.
3.	Public Modules: Explored the Terraform Registry and how to use public modules.
Next Steps
•	Day 4: Deep dive into Terraform state management, remote backends, and state locking.
•	Practice: Try creating your own modules and using them in different projects.
Additional Resources
•	GitHub Repository: Terraform Zero to Hero
•	Terraform Documentation: Terraform Docs
•	Terraform Registry: Terraform Registry
________________________________________
This study material provides a comprehensive overview of Day 3 of the Terraform Zero to Hero series. Use the GitHub repository and practice the examples to solidify your understanding. Happy learning! 🚀

