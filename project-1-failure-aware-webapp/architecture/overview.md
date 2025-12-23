# Architecture Overview

This project demonstrates a production-style AWS web application
architecture with a focus on failure awareness and resilience.

The system is designed to:
- Serve user traffic through an Application Load Balancer
- Run application servers in private subnets
- Scale automatically using an Auto Scaling Group
- Detect and react to both loud and silent failures

The goal of this project is not feature development,
but understanding how real systems behave under failure
and how design decisions affect recovery.
