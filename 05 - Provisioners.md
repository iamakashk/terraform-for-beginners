# Terraform Provisioners — Complete Guide

Terraform Provisioners allow you to execute commands or scripts after a resource is created or destroyed.

> **Warning:** While useful in certain situations, they should be used only as a **last resort** because they break Terraform’s declarative model.

This guide explains provisioners, their types, connection requirements, limitations, alternatives, and real-world examples with diagrams.

---

## Table of Contents
1. [What Are Provisioners?](#1-what-are-provisioners)
2. [Types of Provisioners](#2-types-of-provisioners)
3. [How Provisioners Work (Diagram)](#3-how-provisioners-work-diagram)
4. [Connection Block Explained](#4-connection-block-explained)
5. [Why Provisioners Are Not Recommended](#5-why-provisioners-are-not-recommended)
6. [Better Alternatives](#6-better-alternatives)
7. [Practical Examples](#7-practical-examples)
8. [Full Architecture Diagram](#8-full-architecture-diagram-provisioners-in-pipeline)
9. [Final Recommendations](#9-final-recommendations)

---

## 1. What Are Provisioners?
Provisioners run commands or scripts during the creation or destruction of a resource.

**Common use cases:**
* Bootstrapping a VM
* Uploading files
* Running local scripts
* Calling external systems

*Note: Terraform recommends using them sparingly because they can cause unstable and non-reproducible infrastructure.*

---

## 2. Types of Provisioners

### remote-exec
Runs commands on a remote VM using SSH or WinRM.

```hcl
provisioner "remote-exec" {
  inline = [
    "sudo apt update",
    "sudo apt install -y nginx"
  ]
}
```
## local-exec
$$ Runs commands locally on the system executing Terraform.

Terraform
```hcl
provisioner "local-exec" {
  command = "echo Deployment Completed >> deploy.log"
}
```

## file
$$ Uploads a file or directory to a remote VM.

Terraform
```hcl
provisioner "file" {
  source      = "config.yaml"
  destination = "/tmp/config.yaml"
}
```
## 3. How Provisioners Work (Diagram)
Provisioners run after Terraform creates the resource, not before.

```hcl
Provisioner Workflow

┌─────────────────────┐
│   Terraform Apply   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Resource Created   │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ Run Provisioners    │
│ (file, remote-exec, │
│      local-exec)    │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  Resource Complete  │
└─────────────────────┘
```

## 4. Connection Block Explained
Most provisioners (specifically remote-exec and file) require a connection block.

Example:

Terraform
```hcl
connection {
  type        = "ssh"
  user        = "ec2-user"
  private_key = file("~/.ssh/id_rsa")
  host        = self.public_ip
}
```
Provisioners will fail if:

SSH keys are incorrect.

The VM is not reachable.

Firewalls or Security Groups block access.

## 5. Why Provisioners Are Not Recommended
Terraform discourages provisioners because:

Not idempotent: They cannot be rerun safely; if the command runs twice, it might break the system.

Hard to debug: Logs are often difficult to parse compared to native provider errors.

Failures block resource creation: If a provisioner fails, the resource is marked as "tainted."

Not intended for configuration management: Terraform is for infrastructure, not software configuration.

## 6. Better Alternatives
Use user_data (AWS EC2)
The standard way to bootstrap instances.

Terraform
```hcl
user_data = <<-EOF
#!/bin/bash
apt update
apt install -y nginx
EOF
```
Other Alternatives
Use cloud-init: (Standard for Linux VMs)

Configuration Management Tools: Use Ansible, Chef, or Puppet.

VM Extensions: Use Azure or GCP specific VM extensions.

Provisioners should be your last choice, not your default choice.

## 7. Practical Examples
Example: remote-exec on AWS EC2

Terraform
```hcl
resource "aws_instance" "app" {
  ami           = "ami-123456"
  instance_type = "t2.micro"

  provisioner "remote-exec" {
    inline = [
      "sudo yum install -y httpd",
      "sudo systemctl start httpd"
    ]
  }

  connection {
    type        = "ssh"
    user        = "ec2-user"
    private_key = file("~/.ssh/mykey.pem")
    host        = self.public_ip
  }
}
```
## 8. Full Architecture Diagram (Provisioners in Pipeline)
```hcl

                      ┌─────────────────────────────────┐
                      │      Developer Executes         │
                      │       terraform apply           │
                      └─────────────────────────────────┘
                                     │
                                     ▼
                  ┌────────────────────────────────────┐
                  │  Terraform Creates Infrastructure  │
                  │       (EC2, VM, Bucket, etc.)      │
                  └────────────────────────────────────┘
                                     │
                                     ▼
            ┌────────────────────────────────────────────────┐
            │ Terraform Triggers Provisioners (if defined)   │
            │                                                │
            │  ┌───────────────┐    ┌──────────────────┐     │
            │  │   file        │    │   remote-exec    │     │
            │  └───────────────┘    └──────────────────┘     │
            │          │                      │              │
            │          ▼                      ▼              │
            │   Upload file to VM      Run commands on VM    │
            │                                                │
            └────────────────────────────────────────────────┘
                                     │
                                     ▼
                      ┌─────────────────────────────────┐
                      │    Resource Ready for Use       │
                      └─────────────────────────────────┘
```
