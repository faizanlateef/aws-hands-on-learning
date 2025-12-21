# VPC Basics 

## Why VPC Exists

In AWS, resources should not be launched directly on the internet without control.
A VPC provides a logically isolated network where we can define IP ranges, subnets,
routing rules, and access to the internet.

It allows us to design secure and scalable network architectures similar to
traditional on-premise networks.

---

## What I Built

- Created a **custom VPC** instead of using the default VPC
- Planned and assigned a **CIDR block** for the VPC
- Created a **public subnet** inside the VPC
- Attached an **Internet Gateway (IGW)** to the VPC
- Created and configured a **route table** with internet routing
- Associated the route table explicitly with the public subnet
- Launched an **EC2 instance inside the custom VPC**
- Assigned a **public/elastic IP** to the EC2 instance
- Configured **Security Groups** to allow SSH and HTTP
- Installed **Apache** and verified public web access

This setup represents the **minimum real-world AWS networking foundation**.

---

## CIDR Block Choice

- **VPC CIDR:** `10.0.0.0/16`

Reason for this choice:
- As the subnet mask is /16 providing us 16 host bits eventually giving us 65,536 IPs(2^16)
- Allows easy subnet expansion in the future
- Commonly used in real production environments
- Avoids early IP exhaustion

- **Subnet CIDR:** `10.0.1.0/24`
- Provides 256 IPs (251 usable because 5 are reserved by AWS )
- Suitable for a public subnet hosting web-facing resources

---

## Public vs Private Subnets

- **Public Subnet**
  - The route table of public subnet makes the Public subnet and private subnet different.
  - The route table will have the route to the internet-gateway which will allow the internet access to the subnet 
  - The resources in the subnet can have public/elastic IPs 
  - This subnet is used for web servers , bastion hosts,load balancers


- **Private Subnet**
  - Does NOT have a route to the Internet Gateway
  - Cannot be accessed directly from the internet
  - Requires a NAT Gateway for outbound internet access
  - Used for application servers and databases

Key understanding:
> A subnet is public or private **only because of its route table**, not because of its name.

---

## Routing & Internet Access

- An **Internet Gateway** was attached to the VPC
- A **public route table** was created with the following route:





-This is a new line

