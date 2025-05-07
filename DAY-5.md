### **Study Material: Terraform Zero to Hero Series - Day 5**

---

### **Recap of Day 4**
1. **State File**: Learned how Terraform uses the state file to track infrastructure.
2. **Remote Backend**: Configured S3 as a remote backend to store the state file securely.
3. **State Locking**: Implemented state locking using DynamoDB to prevent conflicts.

---

### **Key Topics Covered in Day 5**
1. **What are Terraform Provisioners?**
2. **Types of Provisioners**
   - **Remote Exec**
   - **Local Exec**
   - **File Provisioner**
3. **Use Cases of Provisioners**
4. **Practical Implementation: Deploying a Python Flask Application**
5. **Debugging and Troubleshooting Provisioners**

---

### **1. What are Terraform Provisioners?**

#### **Definition**
- **Provisioners** in Terraform are used to execute scripts or commands on a resource after it is created or destroyed.
- They allow you to perform additional setup or configuration on resources that Terraform cannot handle natively.

#### **Why Use Provisioners?**
- **Automation**: Automate tasks like installing software, configuring services, or copying files.
- **Flexibility**: Perform actions that are not supported by Terraform’s native resources.
- **Zero-Touch Automation**: Reduce manual intervention by automating post-creation or pre-destruction tasks.

---

### **2. Types of Provisioners**

#### **1. Remote Exec Provisioner**
- **Purpose**: Executes commands on a remote resource (e.g., an EC2 instance) after it is created.
- **Use Case**: Install software, configure services, or run scripts on the remote resource.
- **Example**:
  ```hcl
  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y python3-pip",
      "pip3 install flask",
      "python3 app.py"
    ]
  }
  ```

#### **2. Local Exec Provisioner**
- **Purpose**: Executes commands on the local machine where Terraform is running.
- **Use Case**: Print logs, create files, or trigger local scripts during Terraform execution.
- **Example**:
  ```hcl
  provisioner "local-exec" {
    command = "echo 'Resource created successfully' > output.txt"
  }
  ```

#### **3. File Provisioner**
- **Purpose**: Copies files or directories from the local machine to the remote resource.
- **Use Case**: Deploy application code, configuration files, or scripts to the remote resource.
- **Example**:
  ```hcl
  provisioner "file" {
    source      = "app.py"
    destination = "/home/ubuntu/app.py"
  }
  ```

---

### **3. Use Cases of Provisioners**

#### **1. Application Deployment**
- Use **file provisioners** to copy application code to a remote server and **remote-exec provisioners** to install dependencies and start the application.

#### **2. Configuration Management**
- Use **remote-exec provisioners** to configure services like Nginx, Apache, or Docker on a newly created EC2 instance.

#### **3. Logging and Monitoring**
- Use **local-exec provisioners** to log Terraform execution details or trigger monitoring scripts.

#### **4. Cleanup Tasks**
- Use provisioners to run cleanup scripts before destroying resources (e.g., deleting temporary files or stopping services).

---

### **4. Practical Implementation: Deploying a Python Flask Application**

#### **Steps**
1. **Create a VPC**: Define a VPC with a public subnet.
2. **Create an EC2 Instance**: Launch an EC2 instance in the public subnet.
3. **Deploy the Application**:
   - Use a **file provisioner** to copy the `app.py` file to the EC2 instance.
   - Use a **remote-exec provisioner** to install Python, Flask, and run the application.

#### **Example Code**
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.1.0/24"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id

  provisioner "file" {
    source      = "app.py"
    destination = "/home/ubuntu/app.py"
  }

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y python3-pip",
      "pip3 install flask",
      "python3 /home/ubuntu/app.py"
    ]
  }
}
```

---

### **5. Debugging and Troubleshooting Provisioners**

#### **Common Issues**
1. **Connection Issues**: Ensure the EC2 instance is reachable (e.g., SSH access is configured correctly).
2. **Permission Issues**: Ensure the provisioner has the necessary permissions to execute commands or copy files.
3. **Script Errors**: Debug the script being executed by the provisioner (e.g., check for syntax errors or missing dependencies).

#### **Debugging Steps**
1. **Check Logs**: Review Terraform logs to identify where the provisioner failed.
2. **SSH into the Instance**: Manually SSH into the instance and run the commands to identify issues.
3. **Use `terraform plan`**: Run `terraform plan` to preview the changes and ensure the provisioner is configured correctly.

---

### **Summary of Day 5**
1. **Provisioners**: Learned how to use provisioners to automate tasks on resources.
2. **Types of Provisioners**: Explored remote-exec, local-exec, and file provisioners.
3. **Use Cases**: Understood how provisioners can be used for application deployment, configuration management, and logging.
4. **Practical Implementation**: Deployed a Python Flask application using provisioners.

---

### **Next Steps**
- **Day 6**: Explore advanced Terraform concepts like workspaces, data sources, and dynamic blocks.
- **Practice**: Try using provisioners in your own Terraform projects to automate tasks.

---

### **Additional Resources**
- **GitHub Repository**: [Terraform Zero to Hero](https://github.com/iamveeramalla/terraform-zero-to-hero)
- **Terraform Documentation**: [Terraform Docs](https://www.terraform.io/docs)
- **AWS EC2 Documentation**: [AWS EC2 Docs](https://aws.amazon.com/ec2/)

---

### **Visuals and Tables**

#### **Terraform Provisioner Types**
| **Provisioner Type** | **Purpose**                                                                 | **Example**                                                                 |
|-----------------------|-----------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Remote Exec**       | Execute commands on a remote resource (e.g., EC2 instance).                 | Install software, configure services.                                       |
| **Local Exec**        | Execute commands on the local machine where Terraform is running.           | Print logs, create files, trigger local scripts.                            |
| **File Provisioner**  | Copy files or directories from the local machine to the remote resource.    | Deploy application code, configuration files.                               |

#### **Example: File Provisioner**
```hcl
provisioner "file" {
  source      = "app.py"
  destination = "/home/ubuntu/app.py"
}
```

#### **Example: Remote Exec Provisioner**
```hcl
provisioner "remote-exec" {
  inline = [
    "sudo apt update",
    "sudo apt install -y python3-pip",
    "pip3 install flask",
    "python3 /home/ubuntu/app.py"
  ]
}
```

---

This study material provides a comprehensive overview of Day 5 of the Terraform Zero to Hero series. Use the GitHub repository and practice the examples to solidify your understanding. Happy learning! 🚀


Study Material: Terraform Zero to Hero Series - Day 5
________________________________________
Introduction
•	Instructor: Abhishek Veeramalla
•	Series Name: Terraform Zero to Hero
•	Duration: 7 Days
•	Objective: Deep dive into Terraform provisioners, understand their use cases, and learn how to use them in real-world scenarios.
________________________________________
Recap of Day 4
1.	State File: Learned how Terraform uses the state file to track infrastructure.
2.	Remote Backend: Configured S3 as a remote backend to store the state file securely.
3.	State Locking: Implemented state locking using DynamoDB to prevent conflicts.
________________________________________
Key Topics Covered in Day 5
1.	What are Terraform Provisioners?
2.	Types of Provisioners 
o	Remote Exec
o	Local Exec
o	File Provisioner
3.	Use Cases of Provisioners
4.	Practical Implementation: Deploying a Python Flask Application
5.	Debugging and Troubleshooting Provisioners
________________________________________
1. What are Terraform Provisioners?
Definition
•	Provisioners in Terraform are used to execute scripts or commands on a resource after it is created or destroyed.
•	They allow you to perform additional setup or configuration on resources that Terraform cannot handle natively.
Why Use Provisioners?
•	Automation: Automate tasks like installing software, configuring services, or copying files.
•	Flexibility: Perform actions that are not supported by Terraform’s native resources.
•	Zero-Touch Automation: Reduce manual intervention by automating post-creation or pre-destruction tasks.
________________________________________
2. Types of Provisioners
1. Remote Exec Provisioner
•	Purpose: Executes commands on a remote resource (e.g., an EC2 instance) after it is created.
•	Use Case: Install software, configure services, or run scripts on the remote resource.
•	Example: 
•	provisioner "remote-exec" {
•	  inline = [
•	    "sudo apt update",
•	    "sudo apt install -y python3-pip",
•	    "pip3 install flask",
•	    "python3 app.py &"
•	  ]
•	}
2. Local Exec Provisioner
•	Purpose: Executes commands on the local machine where Terraform is running.
•	Use Case: Print logs, create files, or trigger local scripts during Terraform execution.
•	Example: 
•	provisioner "local-exec" {
•	  command = "echo 'Resource created successfully' > output.txt"
•	}
3. File Provisioner
•	Purpose: Copies files or directories from the local machine to the remote resource.
•	Use Case: Deploy application code, configuration files, or scripts to the remote resource.
•	Example: 
•	provisioner "file" {
•	  source      = "app.py"
•	  destination = "/home/ubuntu/app.py"
•	}
________________________________________
3. Use Cases of Provisioners
1. Application Deployment
•	Use file provisioners to copy application code to a remote server and remote-exec provisioners to install dependencies and start the application.
2. Configuration Management
•	Use remote-exec provisioners to configure services like Nginx, Apache, or Docker on a newly created EC2 instance.
3. Logging and Monitoring
•	Use local-exec provisioners to log Terraform execution details or trigger monitoring scripts.
4. Cleanup Tasks
•	Use provisioners to run cleanup scripts before destroying resources (e.g., deleting temporary files or stopping services).
________________________________________
4. Practical Implementation: Deploying a Python Flask Application
Steps
1.	Create a VPC: Define a VPC with a public subnet.
2.	Create an EC2 Instance: Launch an EC2 instance in the public subnet.
3.	Deploy the Application: 
o	Use a file provisioner to copy the app.py file to the EC2 instance.
o	Use a remote-exec provisioner to install Python, Flask, and run the application in the background.
Example Code
provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "my_vpc" {
  cidr_block = "10.0.0.0/16"
}

resource "aws_subnet" "public_subnet" {
  vpc_id     = aws_vpc.my_vpc.id
  cidr_block = "10.0.1.0/24"
}

resource "aws_instance" "web" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  subnet_id     = aws_subnet.public_subnet.id

  provisioner "file" {
    source      = "app.py"
    destination = "/home/ubuntu/app.py"
  }

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y python3-pip",
      "pip3 install flask",
      "nohup python3 /home/ubuntu/app.py &"
    ]
  }
}
________________________________________
5. Debugging and Troubleshooting Provisioners
Common Issues
1.	Connection Issues: Ensure the EC2 instance is reachable (e.g., SSH access is configured correctly).
2.	Permission Issues: Ensure the provisioner has the necessary permissions to execute commands or copy files.
3.	Script Errors: Debug the script being executed by the provisioner (e.g., check for syntax errors or missing dependencies).
Debugging Steps
1.	Check Logs: Review Terraform logs to identify where the provisioner failed.
2.	SSH into the Instance: Manually SSH into the instance and run the commands to identify issues.
3.	Use terraform plan: Run terraform plan to preview the changes and ensure the provisioner is configured correctly.
________________________________________
Summary of Day 5
1.	Provisioners: Learned how to use provisioners to automate tasks on resources.
2.	Types of Provisioners: Explored remote-exec, local-exec, and file provisioners.
3.	Use Cases: Understood how provisioners can be used for application deployment, configuration management, and logging.
4.	Practical Implementation: Deployed a Python Flask application using provisioners.
________________________________________
Next Steps
•	Day 6: Explore advanced Terraform concepts like workspaces, data sources, and dynamic blocks.
•	Practice: Try using provisioners in your own Terraform projects to automate tasks.
________________________________________
Additional Resources
•	GitHub Repository: Terraform Zero to Hero
•	Terraform Documentation: Terraform Docs
•	AWS EC2 Documentation: AWS EC2 Docs

