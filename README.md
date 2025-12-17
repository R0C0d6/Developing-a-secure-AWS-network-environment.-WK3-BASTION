Building and configuring a secure network environment featuring public and private subnets, proper routing, and restricted, well-managed connectivity.

This project showcases the design and deployment of a secure, segmented network architecture on AWS, aligned with best practices outlined in the AWS Well-Architected Framework. It incorporates network isolation, tightly controlled access, and layered security controls to model a production-ready environment.
The focus is on security, scalability, and observability-core competencies for cloud and DevOps engineers operating in enterprise settings.
    
For this project, the architecture was implemented within a single Availability Zone to make testing and visualization easier. In a real-world, production or enterprise setup, the architecture would be spread across multiple Availability Zones to ensure high availability and improved fault tolerance.

Organizations frequently need to keep private workloads such as databases and application servers—shielded from direct internet exposure while still allowing restricted administrative access.
The task was to model a secure, well-designed AWS VPC setup that applies network segmentation and enforces least-privilege access principles.
APPROACH:
    Design and set up a custom VPC with both public and private subnets.
    Configure routing components, including route tables, an Internet Gateway (IGW), and a NAT Gateway.
    Provision a Bastion Host to enable secure SSH access to resources in private subnets.
    Apply Security Groups and Network ACLs (NACLs) to enforce a defense-in-depth security model.
    Test and confirm proper network isolation and controlled access between components.


ARCHITECTURE DIAGRAM
![Architecture Diagram](https://i.postimg.cc/fbHrg5fT/Whats-App-Image-2025-12-17-at-13-28-11.jpg)


VPC Creation:
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

Configure the route tables:
        Public route table routes 0.0.0.0/0 through the IGW.
        ![RT](https://i.postimg.cc/zv12Lfw5/Screenshot-2025-10-19-114305.png)
        ![RT](https://i.postimg.cc/pXKhYLDz/Screenshot-2025-10-19-114503.png)
        ![RT](https://i.postimg.cc/rpGkFztW/Screenshot-2025-10-19-114551.png)
        ![RT](https://i.postimg.cc/PJP9ZqNM/Screenshot-2025-10-19-114647.png)
        ![RT](https://i.postimg.cc/Y2TMRckw/Screenshot-2025-10-19-114701.png)
        
PRIVATE ROUTE TABLE CREATION
        Private route table routes 0.0.0.0/0 through the NAT Gateway.
        
    Associated subnets accordingly.

![Project Screenshot](https://example.com/image.png)

(Insert Screenshot: Route Table Configuration)

Security Layers (NACLs and Security Groups):

    Network ACLs (NACLs) were configured to explicitly control subnet-level traffic.
        Public subnet allows inbound HTTP(80), HTTPS(443), and SSH(22) traffic.
        Private subnet allows inbound traffic only from the Bastion Host’s subnet.
    Security Groups (SGs):
        Bastion Host SG allows inbound SSH (22), ICMP (ping), and HTTP/HTTPS for testing connectivity.
        Private EC2 SG only allows inbound SSH traffic from the Bastion Host’s SG — not from the internet.

![Project Screenshot](https://example.com/image.png)

(Insert Screenshot: NACL and Security Group Configuration)

Reasoning:
This layered security ensures only trusted traffic flows between defined resources, following the principle of least privilege.
Opening ports 80 and 443 allows web-related testing, and ICMP (ping) helps validate connectivity.
The Bastion Host acts as a controlled entry point — a standard best practice to prevent direct exposure of private workloads.

Bastion Host Configuration and Port Forwarding:

    Deployed a Bastion Host (Amazon Linux EC2 instance) in the public subnet with an Elastic IP for SSH access.
    Connected securely using the private key via SSH.
    Implemented port forwarding to securely access the private EC2 instance.
    For example:

    ssh -i "bastion-key.pem" -L 2222:PRIVATE_EC2_IP:22 ec2-user@BASTION_PUBLIC_IP

        This setup forwards local port 2222 to the private EC2’s SSH port through the Bastion Host, allowing access without direct internet exposure.

![Project Screenshot](https://example.com/image.png)

    (Insert Screenshot: Bastion Host Connection and Port Forwarding Example)

    Testing Conducted:
        Verified connectivity using ping google.com and ping 8.8.8.8 from the private EC2 (via Bastion Host).
        Confirmed that private EC2 instances could reach the internet only through the NAT Gateway.
        Confirmed that no inbound connections were possible from the public internet to the private subnet.

    (Insert Screenshot: Connectivity Tests and Results)

![Project Screenshot](https://example.com/image.png)

Result

    Delivered a fully functional and secure VPC architecture with public and private subnets.
    Verified that network isolation was effectively enforced.
    Established controlled SSH access via a Bastion Host and port forwarding.
    Architecture follows AWS best practices for security, scalability, and operational excellence.
    Demonstrated real-world cloud security implementation readiness for enterprise-scale environments.

Key Design Considerations
Component 	Purpose 	Security/Design Reasoning
VPC & Subnets 	Logical network separation 	Enables isolation of resources
IGW & NAT Gateway 	Internet access control 	Public resources access internet; private instances access updates securely
Bastion Host 	Secure entry point 	Prevents direct SSH exposure of private resources
NACLs 	Subnet-level security 	Adds a stateless layer of filtering
Security Groups 	Instance-level security 	Enforces least privilege and trusted source policy
Routing Tables 	Traffic direction 	Ensures correct packet flow and isolation between subnets
Future Improvements

If this architecture were extended for production:

    Implement multi-AZ deployment for high availability.
    Add VPC Flow Logs for network monitoring and compliance.
    Integrate AWS Systems Manager Session Manager for SSH-less access.
    Incorporate CloudWatch alarms for network performance metrics.
    Enable Transit Gateway for hybrid or multi-VPC environments.

Outcome and Recruiter-Facing Summary (STAR-Aligned)

This project demonstrates not only hands-on AWS implementation but also architectural reasoning, security design, and operational excellence.
By applying AWS best practices, the design achieves network segmentation, least-privilege enforcement, and secure access pathways — principles used by large-scale organizations worldwide.

It validates technical readiness for roles in Cloud Infrastructure, DevOps Engineering, and Solutions Architecture within global cloud-driven environments.
Evidence and Screenshots

Please refer to the following screenshots for full verification of the setup:

    VPC and Subnet Configuration
    Internet and NAT Gateway Setup
    Route Table Configuration
    Security Groups and NACLs
    Bastion Host Connection and Port Forwarding
    Connectivity Tests

![Project Screenshot](https://example.com/image.png)

(Insert all screenshots in corresponding placeholders above)
Repository Structure

Week-3-Secure-Network/ │ ├── diagrams/ │ └── BASHION-ARCHITECTURE.png │ ├── screenshots/ │ ├── vpc-subnets.png │ ├── route-tables.png │ ├── security-groups.png │ ├── nacl-config.png │ ├── bastion-connection.png │ └── connectivity-test.png │ └── README.md
