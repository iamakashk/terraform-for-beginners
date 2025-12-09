# Terraform Providers

## Introduction
Terraform uses **providers** to interact with cloud platforms, SaaS APIs, and on-prem systems.



A provider is responsible for:
* **Authenticating** with a service.
* **Exposing resources** (e.g., `aws_instance`, `azurerm_resource_group`).
* **Handling** create/update/delete operations.

**Examples of popular providers:**
* AWS
* Azure
* Google Cloud
* Kubernetes
* GitHub
* Cloudflare

---

## What Is a Terraform Provider?
A provider is a plugin that Terraform uses to interact with an external system. You declare providers inside your Terraform configuration, usually in a `providers.tf` or `main.tf` file.

Terraform installs providers automatically when you run:

```bash
terraform init
```
## Why Are Providers Important?
----------------------------

1.  They allow Terraform to talk to different platforms and APIs.
    
2.  They define which resources you can create.
    
3.  They manage authentication and API communication.
    
4.  They allow multi-cloud deployments from a single Terraform workflow.

Example: AWS Provider
### 1. Configure the AWS Provider
```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = "ap-south-1"   # Mumbai
}
```
How authentication works:

Terraform automatically uses credentials from:

~/.aws/credentials

Environment variables (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY)

IAM Role (if running on EC2)

### 2. Create an EC2 Instance Using the AWS Provider
```bash
resource "aws_instance" "my_ec2" {
  ami           = "ami-0e96a2a1e4b7aa9c6"
  instance_type = "t2.micro"

  tags = {
    Name = "terraform-demo"
  }
}
```

After running:
```bash
terraform init
terraform apply
```

Terraform will create an EC2 instance in AWS.

Example: Azure Provider
```bash
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 4.0"
    }
  }
}

provider "azurerm" {
  features {}
}
```

Create a Resource Group
```bash
resource "azurerm_resource_group" "rg" {
  name     = "tf-demo-rg"
  location = "East US"
}
```
Example: Google Cloud Provider
```bash
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 5.0"
    }
  }
}

provider "google" {
  project = "my-gcp-project"
  region  = "us-central1"
}
```

Create a Compute Instance
```bash
resource "google_compute_instance" "vm" {
  name         = "tf-demo-vm"
  machine_type = "e2-micro"
  zone         = "us-central1-a"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-12"
    }
  }

  network_interface {
    network = "default"
  }
}
```

Using Multiple Providers in One Project

Terraform supports multi-cloud deployments.

Example: AWS + Azure together
```bash
provider "aws" {
  region = "ap-south-1"
}

provider "azurerm" {
  features {}
}
```

You can now use both providers' resources in the same project.

Provider Version Pinning (Best Practice)

Always specify provider versions to avoid breaking changes:
```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"   # allows 5.x but not 6.x
    }
  }
}
```
Listing All Installed Providers

Run:
```bash
terraform providers
```

You will see all providers used in your project.

# Summary

Providers are plugins that allow Terraform to interact with cloud platforms and APIs.

Each provider exposes its own set of resources.

You must declare providers before using them.

Terraform downloads providers automatically during terraform init.

You can use multiple providers in one configuration.

Always pin provider versions for safety.
