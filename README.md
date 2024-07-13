# AWS Bastion Host & NAT Gateway Configuration

This project demonstrates how to configure a Bastion Host and NAT Gateway in AWS. The setup enables secure access to instances in a private subnet through a Bastion Host in a public subnet, while allowing instances in the private subnet to access the internet via the NAT Gateway.

## Prerequisites

- A VPC with at least two subnets (one public and one private).
- The public subnet has an Internet Gateway attached.

![private-subnet](https://github.com/user-attachments/assets/db6c5066-9d36-40c9-a0c3-f083cb6649d0)

## Steps

### Step 1: Create NAT Gateway

1. **Allocate Elastic IP:**
   - Navigate to the VPC Dashboard in the AWS Management Console.
   - Select "Elastic IPs" from the left-hand menu.
   - Click "Allocate Elastic IP address".

2. **Create NAT Gateway:**
   - In the VPC Dashboard, select "NAT Gateways" from the left-hand menu.
   - Click "Create NAT Gateway".
   - Choose the public subnet for the NAT Gateway.
   - Associate the allocated Elastic IP address with the NAT Gateway.
   - Click "Create NAT Gateway".

### Step 2: Edit Route Tables

1. **Public Subnet Route Table:**
   - Navigate to the Route Tables section within the VPC Dashboard.
   - Select the route table associated with the public subnet.
   - Ensure there is a route directing all internet-bound traffic (0.0.0.0/0) to the Internet Gateway.

2. **Private Subnet Route Table:**
   - Navigate to the Route Tables section within the VPC Dashboard.
   - Select the route table associated with the private subnet.
   - Edit the route table to add a route:
     - **Destination:** 0.0.0.0/0
     - **Target:** NAT Gateway ID (created in Step 1).

### Step 3: Launch EC2 Instances

1. **Launch EC2 in Public Subnet:**
   - Navigate to the EC2 Dashboard in the AWS Management Console.
   - Click "Launch Instance".
   - Choose an Amazon Machine Image (AMI).
   - Select an instance type (e.g., t2.micro).
   - Configure the instance to be within the public subnet of your VPC.
   - Attach a security group allowing SSH access (port 22).

2. **Launch EC2 in Private Subnet:**
   - Follow the same steps as above, but configure the instance to be within the private subnet of your VPC.
   - Attach a security group allowing SSH access (port 22).

### Step 4: Configure Security Groups

1. **Public EC2 Security Group:**
   - Ensure the security group associated with the public EC2 instance allows inbound SSH traffic (port 22) from your IP address.

2. **Private EC2 Security Group:**
   - Ensure the security group associated with the private EC2 instance allows inbound SSH traffic (port 22) from the public EC2 instance's IP address.

### Step 5: SSH into Instances

1. **SSH into Public EC2:**
   - Use your SSH client to connect to the public EC2 instance using the key pair you specified during launch:
     ```sh
     ssh -i /path/to/your-key-pair.pem ec2-user@<public-ec2-public-ip>
     ```

2. **Copy Private Key to Public EC2:**
   - Upload the private key for the private EC2 instance to the public EC2 instance. You can use SCP or any secure file transfer method:
     ```sh
     scp -i /path/to/your-key-pair.pem /path/to/private-key.pem ec2-user@<public-ec2-public-ip>:/home/ec2-user/
     ```

3. **SSH into Private EC2:**
   - From the public EC2 instance, use the private key to SSH into the private EC2 instance:
     ```sh
     ssh -i /home/ec2-user/private-key.pem ec2-user@<private-ec2-private-ip>
     ```
