### **Terraform Zero to Hero Series - Day 7: Secrets Management with HashiCorp Vault**

---

#### **Introduction**
- **Topic**: Secrets Management in Terraform.
- **Importance**: Secrets management is crucial in DevOps for handling sensitive information like passwords, API tokens, certificates, etc.
- **Tools**: HashiCorp Vault is a popular choice for secrets management and integrates well with Terraform, Ansible, CI/CD, and Kubernetes.

---

### **Key Concepts**
1. **Secrets Management**:
   - Handles sensitive information securely.
   - Used across tools like Terraform, Ansible, CI/CD, and Kubernetes.

2. **HashiCorp Vault**:
   - A robust secrets management tool.
   - Supports multiple integrations (Kubernetes, SSH, AWS, etc.).
   - Encrypts sensitive data at rest and in transit.

---

### **Demo Overview**
- **Objective**: Integrate Terraform with HashiCorp Vault to retrieve and use secrets.
- **Steps**:
  1. Set up an EC2 instance on AWS.
  2. Install and configure HashiCorp Vault on the EC2 instance.
  3. Create secrets in Vault.
  4. Integrate Terraform with Vault to retrieve secrets.
  5. Use the retrieved secrets in Terraform to create AWS resources (e.g., EC2 instance with a secret tag).

---

### **Step-by-Step Demo**

#### **1. Set Up EC2 Instance**
- Launch an Ubuntu EC2 instance on AWS.
- Ensure the security group allows SSH (port 22) and Vault (port 8200).

#### **2. Install HashiCorp Vault**
- Update the package manager:
  ```bash
  sudo apt update
  sudo apt install gpg
  ```
- Add HashiCorp repository:
  ```bash
  wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
  echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
  ```
- Install Vault:
  ```bash
  sudo apt update
  sudo apt install vault
  ```

#### **3. Start Vault Server**
- Start Vault in development mode:
  ```bash
  vault server -dev
  ```
- Export the root token and Vault address:
  ```bash
  export VAULT_ADDR='http://127.0.0.1:8200'
  export VAULT_TOKEN='<root-token>'
  ```

#### **4. Create Secrets in Vault**
- Enable the KV (Key-Value) secrets engine:
  ```bash
  vault secrets enable -path=kv kv
  ```
- Create a secret:
  ```bash
  vault kv put kv/test-secret username=admin password=Abhishek
  ```

#### **5. Configure Access and Policies**
- Create a policy for Terraform:
  ```hcl
  path "kv/*" {
    capabilities = ["read"]
  }
  ```
  Save this as `terraform-policy.hcl` and apply:
  ```bash
  vault policy write terraform terraform-policy.hcl
  ```
- Create an AppRole for Terraform:
  ```bash
  vault write auth/approle/role/terraform token_policies="terraform"
  ```
- Retrieve Role ID and Secret ID:
  ```bash
  vault read auth/approle/role/terraform/role-id
  vault write -f auth/approle/role/terraform/secret-id
  ```

#### **6. Integrate Terraform with Vault**
- Add the Vault provider in `main.tf`:
  ```hcl
  provider "vault" {
    address = "http://<vault-ip>:8200"
    skip_child_token = true
    auth_login {
      path = "auth/approle/login"
      parameters = {
        role_id   = "<role-id>"
        secret_id = "<secret-id>"
      }
    }
  }
  ```
- Retrieve the secret using Terraform:
  ```hcl
  data "vault_kv_secret" "example" {
    path = "kv/test-secret"
  }
  ```

#### **7. Use Secrets in Terraform**
- Create an EC2 instance with a tag using the secret:
  ```hcl
  resource "aws_instance" "example" {
    ami           = "ami-0c55b159cbfafe1f0"
    instance_type = "t2.micro"
    tags = {
      Name = data.vault_kv_secret.example.data["username"]
    }
  }
  ```

#### **8. Apply Terraform Configuration**
- Initialize Terraform:
  ```bash
  terraform init
  ```
- Apply the configuration:
  ```bash
  terraform apply
  ```

---

### **Key Takeaways**
1. **Secrets Management**: Essential for securely handling sensitive data.
2. **HashiCorp Vault**: A powerful tool for managing secrets with encryption.
3. **Terraform Integration**: Use the `vault` provider to retrieve and use secrets in infrastructure as code.
4. **AppRole Authentication**: Securely authenticate Terraform with Vault using Role ID and Secret ID.

---

### **GitHub Repository**
- All code and commands are available in the GitHub repository for Day 7.

---

### **Next Steps**
- Practice the integration with other tools like Ansible, Kubernetes, or CI/CD pipelines.
- Explore advanced Vault features like dynamic secrets, PKI, and encryption as a service.

---

### **Conclusion**
- Secrets management is a critical skill for DevOps engineers.
- HashiCorp Vault and Terraform integration simplifies secure handling of sensitive data in infrastructure as code.

---

Terraform Zero to Hero Series - Day 7: Secrets Management with HashiCorp Vault
Introduction
What is Secrets Management?
Secrets management refers to the secure handling of sensitive information such as passwords, API keys, database credentials, and encryption keys. It ensures that these secrets are not hardcoded in source code or exposed in logs.
Why is it Important?
•	Protects sensitive data from unauthorized access.
•	Reduces the risk of security breaches.
•	Ensures compliance with security standards.
•	Automates secure access to secrets for applications.
What is HashiCorp Vault?
HashiCorp Vault is an open-source secrets management tool that provides:
•	Secure storage of secrets.
•	Access control mechanisms.
•	Data encryption at rest and in transit.
•	Integration with Terraform, Kubernetes, CI/CD pipelines, and more.
________________________________________
Key Concepts
Concept	Description
Secrets Management	Securely handling sensitive data like API keys and passwords.
Vault	A tool to store, manage, and access secrets securely.
KV (Key-Value) Engine	A storage method in Vault for saving secrets as key-value pairs.
AppRole Authentication	A method for Terraform to authenticate with Vault using Role ID and Secret ID.
Terraform Integration	Using the Vault provider to retrieve and use secrets in Terraform code.
________________________________________
Step-by-Step Demo
1. Set Up an EC2 Instance on AWS
•	Launch an Ubuntu EC2 instance on AWS.
•	Configure security groups to allow SSH (port 22) and Vault (port 8200).
2. Install HashiCorp Vault
Update the system and install required tools:
sudo apt update
sudo apt install gpg
Add the HashiCorp repository and install Vault:
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt install vault
3. Start Vault Server in Development Mode
vault server -dev
export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN='<root-token>'
4. Create Secrets in Vault
Enable the KV secrets engine:
vault secrets enable -path=kv kv
Store a secret in Vault:
vault kv put kv/test-secret username=admin password=Abhishek
5. Configure Access Policies
Create a Vault policy for Terraform:
path "kv/*" {
  capabilities = ["read"]
}
Save this as terraform-policy.hcl and apply it:
vault policy write terraform terraform-policy.hcl
Create an AppRole for Terraform Authentication:
vault write auth/approle/role/terraform token_policies="terraform"
Retrieve Role ID and Secret ID:
vault read auth/approle/role/terraform/role-id
vault write -f auth/approle/role/terraform/secret-id
6. Integrate Terraform with Vault
Define the Vault provider in main.tf:
provider "vault" {
  address = "http://<vault-ip>:8200"
  skip_child_token = true
  auth_login {
    path = "auth/approle/login"
    parameters = {
      role_id   = "<role-id>"
      secret_id = "<secret-id>"
    }
  }
}
Retrieve the secret using Terraform:
data "vault_kv_secret" "example" {
  path = "kv/test-secret"
}
7. Use Secrets in Terraform
Create an EC2 instance using the secret:
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  tags = {
    Name = data.vault_kv_secret.example.data["username"]
  }
}
8. Apply Terraform Configuration
terraform init
terraform apply
________________________________________
Key Takeaways
1.	Secrets Management: Critical for securing sensitive data.
2.	HashiCorp Vault: Provides secure storage and access to secrets.
3.	Terraform Integration: Automates secret retrieval using the vault provider.
4.	AppRole Authentication: Ensures secure authentication between Terraform and Vault.
________________________________________
Next Steps
•	Explore dynamic secrets in Vault.
•	Integrate with CI/CD pipelines and Kubernetes.
•	Learn about Vault encryption services.
________________________________________
Conclusion
Secrets management is a crucial skill for DevOps professionals. Using Terraform and HashiCorp Vault together provides a secure way to manage secrets in infrastructure automation.
________________________________________
Thank you for reading! 🎉

