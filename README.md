Building and configuring a secure network environment featuring public and private subnets, proper routing, and restricted, well-managed connectivity.

Organizations frequently need to keep private workloads such as databases and application servers, shielded from direct internet exposure while still allowing restricted administrative access.
The task was to model a secure, well-designed AWS VPC setup that applies network segmentation and enforces least-privilege access principles.
This project showcases the design and deployment of a secure, segmented network architecture on AWS, aligned with best practices outlined in the AWS Well-Architected Framework. It incorporates network isolation, tightly controlled access, and layered security controls to model a production-ready environment.

![Architecture Diagram](https://i.postimg.cc/Hsf88M5D/z-Chat-GPT-Image-Dec-17-2025-06-48-02-PM.png)
APPROACH:
    Design and set up a custom VPC with both public and private subnets.
    Configure routing components, including route tables, an Internet Gateway (IGW), and a NAT Gateway.
    Provision a Bastion Host to enable secure SSH access to resources in private subnets.
    Apply Security Groups and Network ACLs (NACLs) to enforce a defense-in-depth security model.
    Test and confirm proper network isolation and controlled access between components.


VPC Creation:
For this project, the architecture was implemented within a single Availability Zone to make testing and visualization easier. In a real-world, production or enterprise setup, the architecture would be spread across multiple Availability Zones to ensure high availability and improved fault tolerance.

Create a custom VPC with a CIDR block of 10.0.0.0/16.
    ![VPC](https://i.postimg.cc/ZYfDbwGd/Screenshot-2025-10-19-111544.png)
    ![VPC](https://i.postimg.cc/8kWB3tTX/Screenshot-2025-10-19-111624.png)
    ![VPC](https://i.postimg.cc/sfwDVRfv/Screenshot-2025-10-19-111649.png)
    
Create two subnets:
Public Subnet: 10.0.1.0/24
![Public Subnet](https://i.postimg.cc/qRFbLRXs/Screenshot-2025-10-19-111837.png)
![Public Subnet](https://i.postimg.cc/GpHSsGvH/Screenshot-2025-10-19-111852.png)
![Public Subnet](https://i.postimg.cc/t4mByJ84/Screenshot-2025-10-19-112133.png)


Private Subnet: 10.0.2.0/24
![Subnet 1](https://i.postimg.cc/5NQ0pR16/Screenshot-2025-10-19-112344.png)
![Subnet 1](https://i.postimg.cc/K4hy0QrB/Screenshot-2025-10-19-112419.png)

Enable Auto Assign Public IP for Public Subnet
![Auto Assign Public IP](https://i.postimg.cc/JhsM4yn6/Screenshot-2025-10-19-112525.png)
![Auto Assign Public IP](https://i.postimg.cc/wBqRWQw1/Screenshot-2025-10-19-112559.png)


Internet Gateway and NAT Gateway:
Create an Internet Gateway:
    ![Create IGW](https://i.postimg.cc/DZsJjv9y/Screenshot-2025-10-19-112705.png)
    ![Create IGW](https://i.postimg.cc/nrpz3vKs/Screenshot-2025-10-19-112717.png)
    
Attach the Internet Gateway (IGW) to the VPC for public internet access.
    ![Attach IGW](https://i.postimg.cc/YC5tX6MM/Screenshot-2025-10-19-112847.png) 
    ![Attach IGW](https://i.postimg.cc/MHNLWYsT/Screenshot-2025-10-19-112909.png)
    
Create an Elastic IP:
    ![Attach EIP](https://i.postimg.cc/k4kZCcYt/Screenshot-2025-10-19-113149.png) 

Deploy a NAT Gateway in the public subnet to allow private instances to access the internet securely for updates or package downloads without being directly exposed. Assign EIP.
![Create NAT Gateway](https://i.postimg.cc/52zKbLWf/Screenshot-2025-10-19-113709.png)
![Create NAT Gateway](https://i.postimg.cc/3JNJSJ58/Screenshot-2025-10-19-113727.png)


Create a Routing Configuration(Route Tables)
PUBLIC ROUTE TABLE CREATION
![RT](https://i.postimg.cc/dDPHyV8v/Screenshot-2025-10-19-113918.png)
![RT](https://i.postimg.cc/zv12Lfw5/Screenshot-2025-10-19-114305.png)

Configure the route table and associate with subnet:
        Public route table routes 0.0.0.0/0 through the IGW.
        ![RT](https://i.postimg.cc/zv12Lfw5/Screenshot-2025-10-19-114305.png)
        ![RT](https://i.postimg.cc/pXKhYLDz/Screenshot-2025-10-19-114503.png)
        ![RT](https://i.postimg.cc/rpGkFztW/Screenshot-2025-10-19-114551.png)
        ![RT](https://i.postimg.cc/PJP9ZqNM/Screenshot-2025-10-19-114647.png)
        ![RT](https://i.postimg.cc/Y2TMRckw/Screenshot-2025-10-19-114701.png)
        
PRIVATE ROUTE TABLE CREATION
![RT](https://i.postimg.cc/m2JQwgpv/Screenshot-2025-10-19-114846.png)
![RT](https://i.postimg.cc/PxVL5Sgs/Screenshot-2025-10-19-115004.png)
    Configure the route table and associate with subnet:
        Private route table routes 0.0.0.0/0 through the NAT Gateway.
        ![RT](https://i.postimg.cc/TYLkGSZ0/Screenshot-2025-10-19-115215.png)
        ![RT](https://i.postimg.cc/yNVXJrbq/Screenshot-2025-10-19-115247.png)
        ![RT](https://i.postimg.cc/XNyH6tMF/Screenshot-2025-10-19-115340.png)  
        
    Associated subnets accordingly.

Security Layers (Security Groups and NACLs):
Security Groups (SGs):
Bastion Host SG allows inbound SSH (22), ICMP (ping), and HTTP/HTTPS for testing connectivity.
        ![Bastion SG](https://i.postimg.cc/4yLN5nJB/Screenshot-2025-10-19-120616.png)
        ![Bastion SG](https://i.postimg.cc/HnX1Jxrp/Screenshot-2025-10-19-120719.png)
        
    
Private EC2 SG only allows inbound SSH traffic from the Bastion Host’s SG - not from the internet.
    ![Private SG](https://i.postimg.cc/vHxczLVZ/Screenshot-2025-10-19-121549.png)
    ![Private SG](https://i.postimg.cc/vm0YMcV2/Screenshot-2025-10-19-121621.png)

NACLs: Network ACLs (NACLs) were configured to explicitly control subnet-level traffic.
    Public subnet allows inbound HTTP(80), HTTPS(443), and SSH(22) traffic.
        ![Public NACL](https://i.postimg.cc/bv4zd46f/Screenshot-2025-10-19-122317.png)
        ![Public NACL](https://i.postimg.cc/1zVSxJyr/Screenshot-2025-10-19-122355.png)
        ![Public NACL](https://i.postimg.cc/MKph2dDS/Screenshot-2025-10-19-122901.png)
        ![Public NACL](https://i.postimg.cc/fR3pky1z/Screenshot-2025-10-19-122915.png)
        ![Public NACL](https://i.postimg.cc/MTz90p3c/Screenshot-2025-10-19-123004.png)
        
Create and Configure Private subnet in a similar way and allow inbound traffic only from the Bastion Host’s subnet.
    ![Private NACL](https://i.postimg.cc/sgD5sHHV/Screenshot-2025-10-19-123148.png)

Edit Subnet Associations and Attach each NACL to the respective subnet
![Subnet Assoc](https://i.postimg.cc/28V38pFx/Screenshot-2025-10-19-124529.png)
![Subnet Assoc](https://i.postimg.cc/YCyC5dmN/Screenshot-2025-10-19-124716.png)
    
This layered security ensures only trusted traffic flows between defined resources, following the principle of least privilege.
Opening ports 80 and 443 allows web-related testing, and ICMP (ping) helps validate connectivity.
The Bastion Host acts as a controlled entry point. This is a standard best practice to prevent direct exposure of private workloads.

Bastion Host Configuration and Port Forwarding:

Deployed a Bastion Host (Amazon Linux EC2 instance) in the public subnet with an Elastic IP for SSH access.
![Bastion EC2](https://i.postimg.cc/j2mr8F1w/Screenshot-2025-10-19-125901.png)
![Bastion EC2](https://i.postimg.cc/C5kVQNpq/Screenshot-2025-10-19-130006.png)
![Bastion EC2](https://i.postimg.cc/PrHGq1kR/Screenshot-2025-10-19-130131.png)
![Bastion EC2](https://i.postimg.cc/7PgcjbTR/Screenshot-2025-10-19-130230.png)
![Bastion EC2](https://i.postimg.cc/RVSP8dmZ/Screenshot-2025-10-19-130350.png)
![Bastion EC2](https://i.postimg.cc/brDvNkc0/Screenshot-2025-10-19-130655.png)



Deployed a Private Server (Amazon Linux EC2 instance) in the private subnet.
![Bastion EC2](https://i.postimg.cc/MTYLMFwr/Screenshot-2025-10-19-130942.png)
![Bastion EC2](https://i.postimg.cc/W3sWvwZ0/Screenshot-2025-10-19-131205.png)
![Bastion EC2](https://i.postimg.cc/TP6QVJtY/Screenshot-2025-10-19-131252.png)
![Bastion EC2](https://i.postimg.cc/JnhNx6Zz/Screenshot-2025-10-19-131508.png)
![Bastion EC2](https://i.postimg.cc/sX8hcV3H/Screenshot-2025-10-19-132054.png)

Connected securely using the private key via SSH.
Implemented port forwarding to securely access the private EC2 instance.
    E.g: ssh -i "bastion-key.pem" -L 2222:PRIVATE_EC2_IP:22 ec2-user@BASTION_PUBLIC_IP
This setup forwards local port 2222 to the private EC2’s SSH port through the Bastion Host, allowing access without direct internet exposure.

![Test](https://i.postimg.cc/L6LMmR02/Screenshot-2025-10-19-142710.png)
![Test](https://i.postimg.cc/gk9fSQc1/Screenshot-2025-10-19-145916.png)
![Test](https://i.postimg.cc/J4cdTh2b/Screenshot-2025-10-19-151728.png)
![Test](https://i.postimg.cc/BnbcxQ9b/Whats-App-Image-2025-12-17-at-13-17-53-(1).jpg)
![Test](https://i.postimg.cc/4dg9FXMm/Whats-App-Image-2025-12-17-at-13-17-53-(2).jpg)

Testing Conducted:
        Verified connectivity using ping google.com and ping 8.8.8.8 from the private EC2 (via Bastion Host).
        Confirmed that private EC2 instances could reach the internet only through the NAT Gateway.
        Confirmed that no inbound connections were possible from the public internet to the private subnet.


### Outcome Summary

A secure and fully operational AWS VPC architecture was successfully implemented, featuring both public and private subnets. Network segmentation and isolation were validated, ensuring private resources were protected from direct internet exposure. Secure administrative access was achieved through a Bastion Host using controlled SSH and port forwarding. Overall, the architecture aligns with AWS best practices for security, scalability, and operational reliability, demonstrating readiness for real-world, enterprise cloud environments.

---

### Design Rationale Overview

| Component                      | Purpose                   | Security & Design Justification                                                               |
| ------------------------------ | ------------------------- | --------------------------------------------------------------------------------------------- |
| VPC & Subnets                  | Network segmentation      | Provides logical isolation of workloads                                                       |
| Internet Gateway & NAT Gateway | Managed internet access   | Public resources access the internet directly; private resources connect securely for updates |
| Bastion Host                   | Secure access point       | Eliminates direct SSH access to private instances                                             |
| Network ACLs                   | Subnet-level controls     | Adds an additional stateless security layer                                                   |
| Security Groups                | Instance-level protection | Enforces least-privilege access from trusted sources                                          |
| Route Tables                   | Traffic management        | Ensures correct routing and isolation between subnets                                         |

---

### Production-Readiness Enhancements
For a production deployment, the architecture could be further strengthened by:

 Deploying resources across multiple Availability Zones for high availability
 Enabling VPC Flow Logs for visibility, monitoring, and compliance
 Using AWS Systems Manager Session Manager to remove the need for SSH
 Configuring CloudWatch alarms to monitor network performance
 Introducing AWS Transit Gateway to support hybrid or multi-VPC connectivity

---

### Overall
This project showcases practical AWS implementation skills alongside strong architectural judgment, security-focused design, and operational awareness. By applying industry-standard AWS best practices, the solution achieves effective network segmentation, least-privilege enforcement, and secure access patterns commonly used in large-scale organizations.
