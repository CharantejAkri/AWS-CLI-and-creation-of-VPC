# 					  AWS CLI

1. #### What is AWS CLI?

   - The **AWS Command Line Interface (AWS CLI)** is an open-source tool that allows you to manage and interact with **Amazon Web Services(AWS)** directly from our terminal.
   - Instead of clicking through buttons in a browser (the **AWS Management Console**), you type text-based commands to control your cloud resources.

2. #### What are the Core Features of AWS CLI?

   - **Unified Tool**: You only need this one tool to manage hundreds of different AWS services, such as launching Amazon EC2 instances or managing Amazon S3 buckets.
   - **Automation**: Because it runs in a terminal, it is ideal for writing **scripts** (like Bash or PowerShell) to automate repetitive tasks.
   - **Cross-Platform**: It works on **Windows, macOS, and Linux**.
   - **Direct API Access**: It provides direct access to the public APIs of AWS services, offering functionality equivalent to the web console.

3. #### Why do we need to use It?

   - **Speed & Efficiency**: Once you learn the commands, it is often faster to type a single line than to navigate through multiple web pages.
   - **Resource Management**: You can manage resources at scale, such as stopping 50 servers at once with a single script.
   - **Reproducibility**: Scripts ensure that infrastructure is set up exactly the same way every time.

4. #### How It Works?

   - **Installation**: You download the [AWS CLI installer](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) for your operating system.
   - **Configuration**: You run `aws configure` to provide your **Access Key ID** and **Secret Access Key**.(**Not Recommended**)
   - **IAM Role Configuration:** The AWS CLI automatically inherits permissions from the attached **IAM Role**, requiring no manual configuration or access keys. If the **server** is from the **AWS** then this method is **Recommended**.
   - **Execution**: You run commands following this structure: `aws <service> <operation> <parameters>`.
     - *Example to list S3 buckets:* `aws s3 ls`.

# 	 		  

# 			 Private Network in AWS

1. #### What is AWS VPC?

   -  A Virtual Private Cloud (VPC) is a logically isolated section of the cloud dedicated to your account. It is your own private network where you define the IP address range and have full control over virtual networking resources.
   - **Function:** It provides complete control over your virtual networking environment, including your own IP address range (CIDR block).
   - **Scope:** A VPC is specific to one AWS Region but spans across all Availability Zones (AZs) within that region.

2. #### What is a **Subnet** in VPC?

   - A **Subnet** is a range of IP addresses in your VPC. You launch resources, like EC2 instances, into specific subnets to group them based on security or operational needs.
   - Subnets are subdivisions of your VPC's IP range. They are used to group resources based on security and operational needs.
   - **Public Subnet:** Used for resources that must be accessible from the internet, like web servers.
   - **Private Subnet:** Used for resources that should never be directly reached from the internet, such as databases.
   - **Scope:** Each subnet resides entirely within one Availability Zone in the Region.

3. #### What is a Route Table in VPC?

   - A **Route Table** contains a set of rules (called routes) that determine where network traffic from your subnet or gateway is directed. Every subnet must be associated with one route table.
   - **Local Route:** Every VPC has a default "local" route that allows all subnets within that VPC to talk to each other.
   - **Custom Routes:** You add custom routes to send traffic to gateways (like an IGW for the internet).

4. #### What is IGW in VPC?

   - The **Internet Gateway(IGW)** is a redundant, highly available VPC component that allows communication between your VPC and the internet. It provides a target in your route tables for internet-bound traffic.
   - It is a horizontally scaled, redundant component that connects your VPC to the public internet.
   - **How it works:** To make a subnet "public," you must add a route in its Route Table that sends internet-bound traffic (0.0.0.0/0) to the IGW.
   - **Relationship:** You can attach only one IGW to a VPC at a time.

5. #### What is Network Security Group(NSG) in VPC?

   - Often referred to simply as a **Security Group**, this acts as a virtual firewall for your **instances** (like EC2).
   - **Stateful:** If you allow an incoming request, the response is automatically allowed back out, even if no outbound rule exists.
   - **Granular:** You can assign different security groups to different instances within the same subnet.
   - **Rules:** You can only specify "Allow" rules; everything else is denied by default.

6. #### What is NACL in VPC?

   - A Network ACL is an optional layer of security that acts as a firewall for controlling traffic in and out of one or more **subnets**.
   - **Stateless:** It does not remember sessions. If you allow traffic in, you must also explicitly create a rule to allow it back out.
   - **Order Matters:** Rules are evaluated in numerical order (lowest to highest). Once a match is found, it is applied, and no further rules are checked.
   - **Rules:** Unlike Security Groups, NACLs support both **Allow** and **Deny** rules (useful for blacklisting specific IP addresses).

7. #### Key Differences: Security Groups vs. NACLs

   | Feature        | Security Group (NSG)                          | Network ACL (NACL)                              |
   | :------------- | :-------------------------------------------- | :---------------------------------------------- |
   | **Layer**      | Second layer of defense (Instance level)      | First layer of defense (Subnet level)           |
   | **State**      | **Stateful** (Remembers connections)          | **Stateless** (Inbound/Outbound rules separate) |
   | **Rules**      | Supports **Allow** rules only                 | Supports **Allow** and **Deny** rules           |
   | **Evaluation** | All rules evaluated before traffic is allowed | Rules evaluated in order by rule number         |

# 	

# 			Creation of VPC in AWS using CLI

#### Steps to Create via AWS CLI:

- Follow these steps in order. Replace placeholders like vpc-id with the actual IDs returned by your terminal.

1. #### **Create the VPC**:

   - `aws ec2 create-vpc --cidr-block 10.0.0.0/16 --tag-specifications "ResourceType=vpc,Tags=[{Key=Name,Value=charan-vpc}]"`

   ![image-20260422160351838](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160351838.png)

   ![image-20260422160524586](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160524586.png)

2. #### Create Subnets (Public and Private):

   **Public Subnet Creation:**

   `aws ec2 create-subnet --vpc-id vpc-id --cidr-block 10.0.1.0/24 --tag-specifications "ResourceType=subnet,Tags=[{Key=Name,Value=charan-public-subnet}]"`

   ![image-20260422160555097](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160555097.png)

   ![image-20260422160618272](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160618272.png)

   **Private Subnet Creation:**

   `aws ec2 create-subnet --vpc-id vpc-id --cidr-block 10.0.2.0/24 --tag-specifications "ResourceType=subnet,Tags=[{Key=Name,Value=charan-private-subnet}]"`

   ![image-20260422160701760](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160701760.png)

   ![image-20260422160716086](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160716086.png)

3. #### **Set up the Internet Gateway (IGW)**:

   `aws ec2 create-internet-gateway --tag-specifications "ResourceType=internet-gateway,Tags=[{Key=Name,Value=charan-igw}]"`

   ![image-20260422160750017](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160750017.png)

   ![image-20260422160949695](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422160949695.png)

   **To attach IGW to VPC:**

   `aws ec2 attach-internet-gateway --vpc-id vpc-id --internet-gateway-id igw-id`

   ![image-20260422161131612](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161131612.png)

   ![image-20260422161143274](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161143274.png)

4. #### **Configure Route Table:**

   #### *<u>**Public Route table:**</u>*

   `aws ec2 create-route-table --vpc-id vpc-id --tag-specifications "ResourceType=route-table,Tags=[{Key=Name,Value=charan-public-rt}]"`

   ![image-20260422161234456](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161234456.png)

   ![image-20260422161246884](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161246884.png)

   **Make it "Public" (Add Internet Route)**:

   `aws ec2 create-route --route-table-id rtb-id --destination-cidr-block 0.0.0.0/0 --gateway-id igw-id`

   ![image-20260422161348449](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161348449.png)

   ![image-20260422161404926](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161404926.png)

   **Attach it to your Public Subnet**:

   `aws ec2 associate-route-table --subnet-id subnet-id --route-table-id rtb-id`

   ![image-20260422161505226](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161505226.png)

   ![image-20260422161529746](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161529746.png)

   #### *<u>Private Route Table</u>*

   `aws ec2 create-route-table --vpc-id vpc-id --tag-specifications "ResourceType=route-table,Tags=[{Key=Name,Value=charan-private-rt}]"`

   ![image-20260422161608655](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161608655.png)

   ![image-20260422161623579](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161623579.png)

   **Attach it to your Private Subnet**:

   `aws ec2 associate-route-table --subnet-id subnet-id --route-table-id rtb-id`

   ![image-20260422161720323](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161720323.png)

   ![image-20260422161737602](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161737602.png)

   ![image-20260422161752270](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161752270.png)

5. #### **Create Security Group and Rules (SSH & HTTP)**:

   `aws ec2 create-security-group --group-name charan-sg --description "charan security group" --vpc-id vpc-id --tag-specifications "ResourceType=security-group,Tags=[{Key=Name,Value=charan-sg}]"`

   ![image-20260422161841917](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161841917.png)

   ![image-20260422161852852](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422161852852.png)

   **<u>*Open SSH (Port 22)*</u>**:

   `aws ec2 authorize-security-group-ingress --group-id sg-id --protocol tcp --port 22 --cidr $(curl -s http://checkip.amazonaws.com)/32`

   ![image-20260422162251992](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162251992.png)

   ![image-20260422162317887](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162317887.png)

   **<u>*Open HTTP (Port 80)*</u>**:

   `aws ec2 authorize-security-group-ingress --group-id sg-id --protocol tcp --port 80 --cidr 0.0.0.0/0`

   ![image-20260422162414300](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162414300.png)

   ![image-20260422162501913](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162501913.png)

6. #### **Create NACL**:

   VPCs come with a default NACL that allows all traffic. To create a custom one:*

   `aws ec2 create-network-acl --vpc-id vpc-id --tag-specifications "ResourceType=network-acl,Tags=[{Key=Name,Value=charan-nacl}]"`

   ![image-20260422162650702](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162650702.png)

   ![image-20260422162708603](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162708603.png)

   **<u>*Add Outbound Rules (Crucial for NACL)*</u>**:

   - Because NACLs are stateless, if you don't allow traffic **out**, the data can't return to the user. You must allow "Ephemeral Ports" (1024-65535).

   `aws ec2 create-network-acl-entry --network-acl-id acl-id --egress --rule-number 100 --protocol tcp --port-range From=1024,To=65535 --cidr-block 0.0.0.0/0 --rule-action allow`

   ![image-20260422162801077](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162801077.png)

   ![image-20260422162842483](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162842483.png)

7. #### Final Verification:

   Run this command to verify all resources created with the **"charan-"** tag:

   `aws ec2 describe-vpcs --filters "Name=tag:Name,Values=charan-*" --query "Vpcs[*].{ID:VpcId,CIDR:CidrBlock}" --output table`

   ![image-20260422162923947](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162923947.png)

   ![image-20260422162943918](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422162943918.png)

8. #### Common Troubleshooting Connection Issues:

   - If you cannot connect to your instance via SSH, check these three common points:

     1. **Internet Gateway (IGW) Attachment**: Ensure your IGW is not just created, but **attached** to your VPC.

        - *Check:* `aws ec2 describe-internet-gateways --filters "Name=attachment.vpc-id,Values=vpc-id"`

        ![image-20260422163225359](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422163225359.png)

     2. **Public Route Table**: Confirm there is a route for `0.0.0.0/0` pointing to the `igw-id`. Without this, the subnet is effectively private.

        - *Check:* `aws ec2 describe-route-tables --route-table-id rtb-id`

        ![image-20260422163319149](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422163319149.png)

        ![image-20260422163357389](C:\Users\MSI 1\AppData\Roaming\Typora\typora-user-images\image-20260422163357389.png)

     3. **Public IP Assignment**: Ensure the EC2 instance has a **Public IP** address. If you didn't enable "Auto-assign public IP" on the subnet, you must manually assign an **Elastic IP**.

        - *Check:* `aws ec2 describe-instances --instance-id i-id --query "Reservations[*].Instances[*].PublicIpAddress"`

     4. If everything looks right but SSH still times out, double-check your **NACL** rules. Remember that NACLs are **stateless**, so they need an outbound rule for ports **1024-65535** to let the SSH response back out!

9. #### Summary of Infrastructure:

   - **VPC**: `charan-vpc` is isolated and ready.
   - **Networking**: Internet Gateway (`charan-igw`) is attached to the Public Route Table.
   - **Security**: `charan-sg` is restricted to **Port 22 (Your IP)** and **Port 80 (Anywhere)**.
   - **Connectivity**: Instances launched in the **Public Subnet** will be reachable via SSH.

10. #### Cleanup (Optional):

    To avoid costs when finished, remember to delete resources in reverse order:

    1. Terminate Instances
    2. Delete Security Group
    3. Detach/Delete IGW
    4. Delete Subnets/VPC

    ------