# Day 1 – Baseline Architecture

## Objective of Day 1

The goal of Day 1 is to build a **clean, working baseline** of a production-style
AWS web application architecture.

At this stage:
- The system is expected to work
- The system is NOT expected to handle failures gracefully
- No resilience mechanisms are implemented yet

This baseline will be intentionally stressed and broken in later days.


## Architectural Overview

User traffic enters the system through an internet-facing Application Load Balancer (ALB) present in the public subnet.Our internal resources like EC2 will not be exposed to the internet

The ALB forwards requests to EC2 application instances running inside private subnets
across multiple Availability Zones.

The EC2 instances do not have public IP addresses and cannot be accessed directly
from the internet.

Outbound internet access from the private instances is provided through a NAT Gateway
located in a public subnet.This ensures no inbound traffic from the internet will access our private instances.

---

## Request Flow (Step-by-Step)

1. A user sends an HTTP request from the internet.
2. The request reaches the Application Load Balancer.
3. The ALB selects a healthy EC2 instance from the target group.
4. The request is forwarded to the EC2 instance inside a private subnet.
5. The application running on EC2 processes the request.
6. The response flows back through the ALB to the user.

At no point does the user directly communicate with the EC2 instances.



## Component Responsibilities

### Application Load Balancer (ALB)
- Acts as the public entry point
- Distributes traffic across instances
- Performs basic health checks
- Does not run application logic

### EC2 Application Instances
- Run the web application code
- Handle business logic
- Live in private subnets
- Do not accept direct internet traffic

### Auto Scaling Group (ASG)
- Ensures a fixed number of instances are running
- Replaces failed instances automatically
- Distributes instances across Availability Zones

### NAT Gateway
- Allows outbound internet access from private instances
- Blocks inbound connections from the internet
- Required for updates and external API calls

---

## What Breaks If a Component Is Missing

- Without ALB: Users cannot access the application as the application itself is in a private subnet and doesn't have a public IP.
- Without EC2 instances: ALB has no targets to send traffic to.
- Without ASG: Instance failures require manual recovery.
- Without NAT Gateway: Application instances cannot reach the internet.
- Without private subnets: Application servers are exposed to the internet.

---

## Key Design Decisions

- EC2 instances are placed in private subnets for security.
- ALB is placed in public subnets to accept internet traffic.
- Multi-AZ deployment is used to reduce single-AZ dependency.
- No resilience or optimization is added at this stage intentionally.
update
