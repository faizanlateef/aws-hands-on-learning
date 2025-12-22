# Route Tables and Traffic Boundaries

## Purpose of Route Tables

Route tables define how traffic flows between subnets and outside the VPC.

They do not provide security by themselves but determine **where traffic is allowed
to go** at the network level.

Security Groups decide **who is allowed**.
Route Tables decide **where traffic can flow**.

---

## Public Route Table

The public route table is associated with public subnets and allows direct
internet access.

### Routes

- `10.0.0.0/16` → local  (This allows the subnet to route within the VPC .Notice the CIDR we chose for VPC)
- `0.0.0.0/0` → Internet Gateway

### Associated Subnets

- public-a
- public-b

This configuration allows resources in public subnets to:
- allows resources to communicate within VPC
- Receive traffic from the internet
- Send traffic to the internet

  


## Private Route Table

The private route table is associated with private subnets and does not allow
direct internet access.

### Routes

- `10.0.0.0/16` → local  
- `0.0.0.0/0` → NAT Gateway

### Associated Subnets

- private-a
- private-b

This configuration allows private resources to:
- Communicate within the VPC
- Initiate outbound connections to the internet
- Reject direct inbound connections from the internet(thanks to NAT gateway which alters the source and destination IP of the packets accordingly)



## Traffic Boundary Summary

- Internet traffic can reach only the Application Load Balancer.
- EC2 instances in private subnets cannot be accessed directly from the internet.
- Outbound internet access from private instances is controlled through the NAT Gateway.
- All inbound access to application instances must pass through the ALB.

## What Breaks If Routing Is Incorrect

- If public subnets do not have a route to the Internet Gateway, the ALB is unreachable.
- If private subnets route directly to the Internet Gateway, application servers are exposed.
- If private subnets do not have a NAT route, instances cannot access the internet.
