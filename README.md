# AWS Bastion Host Infrastructure

This repository contains the documentation and configuration steps for setting up a secure Bastion Host (Jump Box) on AWS to manage private instances.

## Architecture Overview
The architecture consists of a custom VPC divided into public and private layers:
- **Public Subnet**: Houses the Bastion Host with an Elastic IP and Internet Gateway access.
- **Private Subnet**: Houses sensitive resources (e.g., App Servers, Databases) with no direct internet access.
- **Security Groups**: Strictly configured to allow SSH (Port 22) only from specific source IPs.

## Getting Started

### Prerequisites
- An active **AWS Account**.
- **SSH Key Pair** (.pem file) created in the AWS EC2 Console.
- A local terminal with **SSH client** installed.

### Step 1: Network Setup
1. Create a **VPC** with a CIDR block (e.g., `10.0.0.0/16`).
2. Create two subnets:
   - **Public Subnet** (e.g., `10.0.1.0/24`)
   - **Private Subnet** (e.g., `10.0.2.0/24`)
3. Attach an **Internet Gateway** to the VPC and update the Public Route Table to point `0.0.0.0/0` to it.

### Step 2: Security Group Configuration
- **Bastion-SG**: Inbound SSH (22) from your local **Public IP** only.
- **Private-SG**: Inbound SSH (22) only from the **Bastion-SG ID**.

### Step 3: Deployment
1. Launch an EC2 instance in the **Public Subnet** (Bastion).
2. Launch an EC2 instance in the **Private Subnet** (Internal Server).

## How to Connect

### The Traditional Method (Key Copy)
1. **Upload your key** to the Bastion:
   ```bash
   scp -i "your-key.pem" your-key.pem ec2-user@BASTION_PUBLIC_IP:~/.
   ```
2. **SSH into Bastion**:
   ```bash
   ssh -i "your-key.pem" ec2-user@BASTION_PUBLIC_IP
   ```
3. **Set Key Permissions**:
   ```bash
   chmod 400 your-key.pem
   ```
4. **Jump to Private Server**:
   ```bash
   ssh -i "your-key.pem" ec2-user@PRIVATE_IP
   ```

## Security Best Practices
- **Never** leave your private key on the Bastion host permanently; run `rm ~/your-key.pem` after your session.
- Regularly rotate your SSH keys and audit Security Group rules.
- Consider using **AWS Systems Manager (SSM)** for a keyless, more secure alternative to traditional bastions.
