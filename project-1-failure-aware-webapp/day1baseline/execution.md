# Baseline Execution


## Objective

The objective of Day 1 was to build a clean, working baseline
of a production-style AWS web application architecture.

At this stage:
- The system is expected to work correctly
- The system is not expected to handle failures gracefully.
- No resilience or optimization logic is implemented yet.

This baseline is intentionally simple ,so that the failure can be simulated and observed.




## Architecture Scope

The baseline architecture consists of:

- A dedicated VPC with public and private subnets across multiple AZs
- An internet-facing Application Load Balancer (ALB)
- EC2 application instances running in private subnets
- An Auto Scaling Group managing instance lifecycle
- A NAT Gateway providing outbound internet access
- Security groups enforcing controlled traffic flow

No databases,resilience mechanisms
are included at this stage.



## Request Flow

1. A user sends an HTTP request to the public ALB DNS name.
2. The Application Load Balancer receives the request.
3. ALB forwards the request to a healthy EC2 instance
   in a private subnet via the target group.
4. The Flask application running on EC2 processes the request.
5. The response is returned to the user through the ALB.

At no point are EC2 instances directly exposed to the internet.
All inbound traffic flows through the load balancer.





## Application Setup

A lightweight Flask application was deployed on each EC2 instance
using instance user data.

Key characteristics:
- The application listens directly on port 80
- No reverse proxy (nginx) is used at this stage
- Each instance returns its hostname in the response
  to visibly confirm load balancing behavior

This approach keeps the baseline simple
and reduces complexity to observe failure.




## Issues Encountered and Resolutions

### Unhealthy Targets in Target Group

Initially, all EC2 instances appeared as unhealthy
in the ALB target group, resulting in 502 errors
when accessing the load balancer DNS.

#### Root Cause
The EC2 security group did not allow inbound HTTP traffic
from the ALB security group.
As a result, the load balancer could not reach the instances
even though the application was running.

#### Resolution
The EC2 security group was updated to allow inbound
HTTP (port 80) traffic exclusively from the ALB security group.





## Baseline Validation

The baseline was validated using the following checks:

- Target group showed all instances as healthy
- ALB DNS returned application responses successfully
- Refreshing the page showed different hostnames,
  confirming traffic distribution across instances
- EC2 instances had no public IP addresses

These checks confirmed that networking,
load balancing, and application setup were correct.



## Outcome 

By the end, a fully functional baseline architecture
was successfully deployed.

The system:
- Served real traffic through an ALB
- Ran application instances in private subnets
- Scaled and replaced instances via ASG
- Enforced controlled and explainable traffic flow

This baseline served as the foundation
for all subsequent failure and resilience experiments.

