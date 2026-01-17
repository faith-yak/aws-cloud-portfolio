\# Building and Troubleshooting a Custom AWS VPC with Public EC2 Access

This project demonstrates the design, deployment, and troubleshooting of a custom AWS VPC with a public subnet hosting an Amazon Linux 2023 EC2 instance running NGINX.

The goal was to intentionally move away from the AWS default VPC and build networking components from scratch, understand their interactions, and resolve real-world connectivity issues related to stateless network controls, outbound internet access, and first-boot package installation.

\---

\#\# Architecture Overview

\- Custom VPC: \`10.0.0.0/16\`  
\- Public Subnet: \`10.0.1.0/24\` (us-east-1a)  
\- Internet Gateway attached to the VPC  
\- Custom route table associated with the public subnet  
\- Security Group controlling instance-level traffic  
\- Network ACL controlling subnet-level traffic  
\- Amazon Linux 2023 EC2 instance with NGINX installed via user data

\---

\#\# Services and Technologies Used

\- AWS VPC  
\- Subnets  
\- Route Tables  
\- Internet Gateway  
\- Security Groups (stateful firewall)  
\- Network ACLs (stateless firewall)  
\- EC2 (Amazon Linux 2023\)  
\- Cloud-init / User Data  
\- NGINX  
\- Linux networking and package management (dnf/yum)

\---

\#\# Key Implementation Steps

1\. Created a custom VPC with CIDR \`10.0.0.0/16\`  
2\. Created a public subnet with CIDR \`10.0.1.0/24\`  
3\. Enabled auto-assign public IPv4 on the subnet  
4\. Created and attached an Internet Gateway  
5\. Created a route table with \`0.0.0.0/0 → IGW\`  
6\. Associated the route table with the public subnet  
7\. Created a Security Group allowing SSH (22) and HTTP (80)  
8\. Created a custom Network ACL with explicit inbound and outbound rules  
9\. Launched an Amazon Linux 2023 EC2 instance  
10\. Installed and configured NGINX via user data

\---

\#\# Challenges Encountered

\#\#\# SSH Connectivity Failure  
Initially, SSH access failed despite correct inbound rules.    
Root cause was incomplete Network ACL configuration. Because NACLs are stateless, return traffic using ephemeral ports was blocked.

\#\#\# User Data and System Update Failures  
NGINX installation and system updates failed due to missing outbound HTTPS (443) and ephemeral port access. Amazon Linux 2023 retrieves repositories from Amazon S3 over HTTPS, which requires both outbound 443 and ephemeral ports for return traffic.

\---

\#\# Final Network ACL Configuration

\#\#\# Inbound Rules  
\- SSH (22)  
\- HTTP (80)  
\- HTTPS (443)  
\- Ephemeral ports (1024–65535)

\#\#\# Outbound Rules  
\- HTTP (80)  
\- HTTPS (443)  
\- Ephemeral ports (1024–65535)

\---

\#\# Result

\- SSH access works reliably  
\- NGINX installs successfully on first boot  
\- System updates complete without timeouts  
\- Web server is publicly accessible over HTTP

\---

\#\# Screenshots

\- VPC and Subnet configuration  
\- Route table association  
\- Security Group rules  
\- Network ACL rules  
\- EC2 instance details  
\- Browser access to NGINX welcome page  
\- Successful \`yum update\` output

(Screenshots are located in the \`/screenshots\` directory.)

\---

\#\# Key Takeaways

\- Default VPCs hide many networking requirements  
\- Network ACLs require explicit handling of ephemeral ports  
\- Outbound HTTPS is critical for Amazon Linux 2023 package management  
\- Security Groups and NACLs serve very different purposes  
\- Troubleshooting cloud networking requires understanding packet flow, not just console settings

\---

\#\# Author

Faith Yakubu    
Cloud Security / Infrastructure Engineering  
