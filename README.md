## AWS VPC Infrastructure via CLI
This project documents the creation of a secure, tagged AWS VPC environment using the AWS Command Line Interface.
## Architecture Overview

* VPC: charan-vpc (10.0.0.0/16)
* Subnets: Public (Internet facing) and Private (Isolated).
* Security: Restricted SSH access (My IP only) and Open HTTP.
* Gateways: Internet Gateway (IGW) for public connectivity.

## Step-by-Step Implementation## 1. Networking Setup
Create the VPC and the "City Gate" (Internet Gateway).

# Create VPC
aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications "ResourceType=vpc,Tags=[{Key=Name,Value=charan-vpc}]"
# Create and Attach IGW
aws ec2 create-internet-gateway --tag-specifications "ResourceType=internet-gateway,Tags=[{Key=Name,Value=charan-igw}]"
aws ec2 attach-internet-gateway --internet-gateway-id <igw-id> --vpc-id <vpc-id>

## 2. Routing
Configure the "GPS" of the network to allow internet traffic.

# Create Route Table
aws ec2 create-route-table --vpc-id <vpc-id> --tag-specifications "ResourceType=route-table,Tags=[{Key=Name,Value=charan-public-rt}]"
# Add Route to Internet
aws ec2 create-route --route-table-id <rtb-id> --destination-cidr-block 0.0.0.0/0 --gateway-id <igw-id>

## 3. Security Group (Firewall)
Apply the "Stateful" firewall rules. Note that SSH is restricted to your specific machine for security.

# Create SG and open ports in one line
aws ec2 create-security-group --group-name charan-sg --description "charan security group" --vpc-id <vpc-id> --tag-specifications "ResourceType=security-group,Tags=[{Key=Name,Value=charan-sg}]"
# Restrict SSH to My IP
aws ec2 authorize-security-group-ingress --group-id <sg-id> --protocol tcp --port 22 --cidr $(curl -s http://amazonaws.com)/32
# Open HTTP to Everyone
aws ec2 authorize-security-group-ingress --group-id <sg-id> --protocol tcp --port 80 --cidr 0.0.0.0/0

## Verification Checklist
Run these to ensure everything is connected properly:

* Verify Identity/Role: aws sts get-caller-identity (Uses attached IAM role automatically).
* Verify IGW Attachment: aws ec2 describe-internet-gateways --filters "Name=attachment.vpc-id,Values=<vpc-id>"
* Verify Tags: aws ec2 describe-vpcs --filters "Name=tag:Name,Values=charan-*"

## Troubleshooting

* Stateless Blocks: If SSH times out, ensure your NACL allows outbound ephemeral ports (1024-65535).
* Connection Denied: Ensure you are using ://amazonaws.com to whitelist your IP; standard amazonaws.com will return an HTML error and break the CIDR string.
