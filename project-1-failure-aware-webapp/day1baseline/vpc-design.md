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

The VPC is divided into multiple subnets to separate internet-facing resources
from internal application components.

The subnet design follows these principles:
- Public and private subnets are created in pairs
- Each pair spans a different Availability Zone
- Subnets are sized to allow growth without unnecessary waste

### Planned Subnets

| Subnet Name | Type    | CIDR Block   | Availability Zone |
|------------|---------|--------------|-------------------|
| public-a   | Public  | 10.0.1.0/24  | AZ-A              |
| public-b   | Public  | 10.0.2.0/24  | AZ-B              |
| private-a  | Private | 10.0.11.0/24 | AZ-A              |
| private-b  | Private | 10.0.12.0/24 | AZ-B              |

Each subnet uses a `/24` CIDR, providing 256 IP addresses, which is sufficient
for application workloads while keeping the network layout simple and readable.

Larger subnets can be created based on project requirements by choosing a
smaller subnet prefix. For example, a `/20` subnet provides 4,096 IP addresses.

Subnet pairing across Availability Zones is critical. If all private subnets
exist in a single AZ, an AZ failure would make all application instances
unreachable, even if the load balancer is still available.

Subnet CIDR gaps are intentional to allow future subnet insertion without
restructuring the existing network.