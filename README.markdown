# Cloud PRT June 2025 - Advanced AWS Infrastructure Project

![AWS Infrastructure Banner](https://img.shields.io/badge/AWS-Cloud%20Infrastructure-orange?logo=amazonaws)  
![Project Status](https://img.shields.io/badge/Status-Completed-green)  
![Region](https://img.shields.io/badge/Region-US--West--2%20(Oregon)-blue)

## 📌 Project Overview
This project showcases advanced **AWS Cloud** and **DevOps** skills by designing and implementing a secure, scalable, and isolated infrastructure in the AWS Oregon (us-west-2) region. The setup includes custom Virtual Private Clouds (VPCs), EC2 instances, VPC peering, and an S3 Gateway VPC Endpoint, adhering to best practices for network segmentation, security, and resource optimization. The project demonstrates proficiency in AWS networking, compute services, and secure data access, with comprehensive documentation and visual evidence.

---

## 🎯 Objectives
The project meets the following requirements:
1. **Create Custom VPCs**:
   - **PRT-App-VPC**: Hosts application workloads.
   - **PRT-Data-VPC**: Manages data-related services.
2. **Subnet Configuration**: Each VPC includes one public and one private subnet for isolation.
3. **EC2 Instance Deployment**:
   - **App-Instance**: Launched in the private subnet of PRT-App-VPC.
   - **Data-Instance**: Launched in the private subnet of PRT-Data-VPC.
4. **Security Group Configuration**: Restrict App-Instance to communicate only with Data-Instance.
5. **VPC Peering**: Establish connectivity between private subnets of both VPCs.
6. **S3 Gateway VPC Endpoint**: Enable Data-Instance to access S3 securely without internet access.
7. **Documentation**: Provide screenshots to validate the setup and configuration.

---

## 🛠 Tools and Technologies
- **AWS Services**:
  - **VPC**: For network isolation and segmentation.
  - **EC2**: For compute resources.
  - **S3**: For object storage access.
  - **VPC Peering**: For inter-VPC communication.
  - **VPC Endpoint**: For private S3 access.
  - **Security Groups**: For network-level access control.
- **Region**: us-west-2 (Oregon)
- **Operating System**: Amazon Linux 2
- **CLI Tools**: AWS CLI for validation and testing
- **Best Practices**: Infrastructure as Code (IaC) principles, network security, and least privilege access

---

## 🏗 Project Architecture
The architecture is designed to ensure **security**, **scalability**, and **efficiency**. Below is a high-level overview of the infrastructure:

```
+---------------------+            +---------------------+
|   PRT-App-VPC      |            |   PRT-Data-VPC      |
| (10.0.0.0/16)      |            | (10.1.0.0/16)      |
|                    |            |                    |
|  +--------------+  |            |  +--------------+  |
|  | Public Subnet|  |            |  | Public Subnet|  |
|  | 10.0.1.0/24  |  |            |  | 10.1.1.0/24  |  |
|  +--------------+  |            |  +--------------+  |
|  | Private Subnet|  |  VPC Peering  | Private Subnet|  |
|  | 10.0.2.0/24  |<----->PCX<----->| 10.1.2.0/24  |  |
|  |  App-Instance|  |            |  | Data-Instance|  |
|  +--------------+  |            |  +--------------+  |
|                    |            |  | S3 Gateway     |  |
|                    |            |  | VPC Endpoint   |  |
|                    |            |  +--------------+  |
+---------------------+            +-------|-------------+
                                  |
                                  v
                             +------------------+
                             | AWS S3 (Private) |
                             +------------------+
```

---

## 🔧 Implementation Details

### 1. VPC Configuration
**Objective**: Create two isolated VPCs for application and data workloads.  
**Explanation**:
- VPCs provide network isolation, ensuring resources are segmented for security and scalability.
- Each VPC is assigned a unique CIDR block to avoid IP overlap.
- Public subnets host resources requiring internet access (e.g., NAT Gateway), while private subnets host sensitive workloads (e.g., EC2 instances).
- DNS hostnames and resolution are enabled for seamless resource communication.

**Configuration**:
- **PRT-App-VPC**:
  - CIDR: 10.0.0.0/16
  - Public Subnet: 10.0.1.0/24
  - Private Subnet: 10.0.2.0/24
- **PRT-Data-VPC**:
  - CIDR: 10.1.0.0/16
  - Public Subnet: 10.1.1.0/24
  - Private Subnet: 10.1.2.0/24

**Screenshot**:
- **VPC Resource Maps**: Visualizes the VPC structure, subnets, and route tables.  
  ![VPC Resource Map]
---

### 2. EC2 Instance Deployment
**Objective**: Launch EC2 instances in private subnets for secure workload hosting.  
**Explanation**:
- Amazon Linux 2 instances are used for lightweight, secure compute resources.
- Instances are placed in private subnets to prevent direct internet exposure.
- Key pairs and Security Groups are configured for secure access and communication.

**Configuration**:
- **App-Instance**:
  - VPC: PRT-App-VPC
  - Subnet: Private (10.0.2.0/24)
  - Instance Type: t2.micro (or similar)
- **Data-Instance**:
  - VPC: PRT-Data-VPC
  - Subnet: Private (10.1.2.0/24)
  - Instance Type: t2.micro (or similar)

**Screenshot**:
- **Instance Summary**: Shows instance details, including IDs, IPs, and subnet assignments.  
  

---

### 3. Security Group Configuration
**Objective**: Restrict App-Instance to communicate only with Data-Instance.  
**Explanation**:
- Security Groups act as virtual firewalls, controlling inbound and outbound traffic at the instance level.
- The principle of least privilege is applied to minimize attack surfaces.
- App-Instance’s Security Group references Data-Instance’s private IP or Security Group ID for precise access control.

**Configuration**:
- **App-Instance Security Group**:
  - Inbound: Allow traffic from Data-Instance (e.g., SSH on port 22, ICMP for ping).
  - Outbound: Allow traffic to Data-Instance only.
- **Data-Instance Security Group**:
  - Inbound: Allow traffic from App-Instance.
  - Outbound: Allow necessary traffic (e.g., to S3 endpoint).

**Screenshot**:
- **Security Group Rules**: Displays rules for both instances.  
  

---

### 4. VPC Peering Connection
**Objective**: Enable private communication between VPCs.  
**Explanation**:
- VPC Peering allows direct communication between VPCs without traversing the public internet.
- Route tables in both VPCs are updated to route traffic through the peering connection (PCX).
- This setup ensures low-latency, secure communication between private subnets.

**Configuration**:
- Created a peering connection between PRT-App-VPC and PRT-Data-VPC.
- Updated route tables:
  - PRT-App-VPC Private Subnet: Add route for 10.1.0.0/16 via PCX.
  - PRT-Data-VPC Private Subnet: Add route for 10.0.0.0/16 via PCX.

**Screenshot**:
- **VPC Peering Connection**: Shows peering status and route table entries.  
  ![VPC Peering](screenshots/vpc-peering.png)

---

### 5. S3 Gateway VPC Endpoint
**Objective**: Enable Data-Instance to access S3 privately.  
**Explanation**:
- A Gateway VPC Endpoint allows private access to S3 without an internet gateway or NAT device.
- The endpoint is associated with the private subnet’s route table, routing S3 traffic internally.
- This setup enhances security by keeping data within the AWS network.

**Configuration**:
- Created an S3 Gateway VPC Endpoint in PRT-Data-VPC.
- Associated the endpoint with the private subnet’s route table (10.1.2.0/24).
- Configured endpoint policy to allow access to specific S3 buckets (if applicable).

**Screenshot**:
- **VPC Endpoint Configuration**: Shows endpoint details and route table association.  
  ![VPC Endpoint](screenshots/vpc-endpoint.png)

---

### 6. Testing and Validation
**Objective**: Verify connectivity and access controls.  
**Explanation**:
- Connectivity between App-Instance and Data-Instance was tested using tools like `ping` or `ssh` (based on Security Group rules).
- S3 access from Data-Instance was validated using AWS CLI commands (e.g., `aws s3 ls`) to confirm private access via the VPC endpoint.
- Tests ensure compliance with security requirements and functionality.

**Screenshots**:
- **App-Instance Communication**: Demonstrates successful communication with Data-Instance.  
  ![Communication Test](screenshots/communication-test.png)
- **S3 Access from Data-Instance**: Shows AWS CLI output for S3 access.  
  ![S3 Access](screenshots/s3-access.png)

---

## 📂 Repository Structure
```
Cloud-PRT-June-2025/
├── README.md
└── screenshots/
    ├── vpc-resource-maps.png
    ├── instance-summary.png
    ├── vpc-peering.png
    ├── vpc-endpoint.png
    ├── communication-test.png
    ├── s3-access.png
    ├── security-groups.png
```

---

## 🚀 Advanced Features and Optimizations
- **Scalability**: The VPC design supports additional subnets and instances for future growth.
- **Security**: Security Groups and private subnets ensure minimal exposure to external threats.
- **Cost Optimization**: Used t2.micro instances and Gateway Endpoints to minimize costs.
- **Monitoring**: Configured instances to support CloudWatch monitoring (not shown in screenshots but recommended for production).
- **IaC Potential**: The setup can be automated using AWS CloudFormation or Terraform for repeatability.

---

## 📸 Screenshots
The following screenshots provide visual evidence of the infrastructure setup:

1. **VPC Resource Maps**  
   ![VPC Resource Map]
2. **Instance Summary**  
   ![Instance Summary]
3. **VPC Peering Connection**  
   ![VPC Peering]
4. **VPC Endpoint Configuration**  
   ![VPC Endpoint]
5. **App-Instance Communication**  
   ![Communication Test]
6. **S3 Access from Data-Instance**  
   ![S3 Access]
7. **Security Group Rules**  
   ![Security Groups]
---

## 📋 How to Replicate
1. **Clone the Repository**:
   ```bash
   git clone https://github.com/your-username/Cloud-PRT-June-2025.git
   ```
2. **Review Screenshots**: Check the `screenshots/` folder for visual documentation.
3. **Recreate the Setup**:
   - Follow the steps in the **Implementation Details** section using the AWS Management Console.
   - Use AWS CLI or SDKs for advanced automation (optional).
4. **Validate**: Test connectivity and S3 access as described in the **Testing and Validation** section.

---

## 💡 Lessons Learned
- **Network Design**: Importance of CIDR planning to avoid IP conflicts in VPC peering.
- **Security**: Using Security Groups and VPC Endpoints to enforce least privilege access.
- **Troubleshooting**: Debugging route table and Security Group misconfigurations for connectivity.
- **Best Practices**: Applying AWS Well-Architected Framework principles for security and cost efficiency.

---

## 📬 Contact
For inquiries or collaboration opportunities, reach out via:  
- **Email**: ayukeshava@gmail.com  
- **LinkedIn**: https://www.linkedin.com/in/channakeshava-b-l-94552a324  
- **GitHub**: https://github.com/ayukeshava1

---

## 🔗 References
- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/)
- [AWS EC2 User Guide](https://docs.aws.amazon.com/ec2/)
- [AWS S3 Gateway Endpoint Guide](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-s3.html)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
