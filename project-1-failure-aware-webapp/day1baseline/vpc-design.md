# VPC and Subnet Design 

## VPC CIDR Choice

For this project, a dedicated VPC is created with the CIDR block `10.0.0.0/16`which is important in production to create an isolated network for our system

/16 means we have 16 network bits and 16 host bits ,providing us enough IP addresses(2^16) that can be subdivided into multiple subnets without running out of IP addresses


Using a `/16` CIDR allows flexibility for:
- Public subnets
- Private subnets
- Future expansion (databases, caches, internal services)

The CIDR range is chosen deliberately , to avoid IP exhaustion in real-world scenarios 
and to maintain clear network boundaries as the architecture grows.

CIDR planning is one of the crucial steps in a real-world project as different services are added over time and if the IPs get exhausted ,we are compelled to rebuild everything 

## Subnet Layout Plan
