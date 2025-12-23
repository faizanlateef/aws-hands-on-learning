# Day 3 – Resilience, Health Checks, and Silent Failures

This stage focuses on a critical failure: silent failure.

The objective is to observe how a system can appear healthy
to the platform while delivering a poor user experience,
and to explore how health check design affects recovery behavior.

## Problem: Healthy but Broken

The application was intentionally modified to introduce
significant response latency in the main request path.

Despite this degradation:
- EC2 instances remained in a running state
- The Application Load Balancer continued to report healthy targets
- The Auto Scaling Group did not replace any instances

From the platform’s perspective, the system was healthy.
From the user’s perspective, the application was effectively unusable.

## Why the Failure Was Not Detected

The ALB health check was configured to call a lightweight
`/health` endpoint that returned HTTP 200 immediately.

The performance degradation existed only in the main
application request path (`/`), which the health check
did not evaluate.

As a result:
- Health checks validated process availability
- They did not reflect real user experience
- The platform had no signal that the application was degraded


## Silent Failure vs Loud Failure

Loud failures, such as instance termination, are easy to detect.
They trigger automated recovery mechanisms and alerts.

Silent failures are more dangerous because:
- The system appears healthy
- No automated recovery is triggered
- Users experience degraded service
- The issue may persist indefinitely without intervention

In this scenario, the platform behaved exactly as configured,
but the configuration did not represent actual application health.

## Making Health Checks Represent Reality

To convert silent failure into detectable failure,
the health check was redesigned to reflect application responsiveness.

The health endpoint was modified to fail
when response time exceeded an acceptable threshold.

This established a clear contract:
If the application cannot respond within defined limits,
it should not receive user traffic.


## Load Balancer Health vs Auto Scaling Decisions

The Application Load Balancer uses health checks
to decide where to route traffic.

The Auto Scaling Group uses health checks
to decide when to replace instances.

By default, the Auto Scaling Group evaluates only EC2-level health.
It does not consider ALB target health unless explicitly configured to do so.

This separation explains why:
- ALB stopped routing traffic to degraded instances
- ASG did not replace them until configured to trust ALB health


## Enabling Fail-Fast Recovery

The Auto Scaling Group health check type
was changed from EC2 to ELB.

This explicitly instructed the ASG to trust
application-level health signals when making lifecycle decisions.

As a result:
- Degraded instances were marked unhealthy
- Traffic was removed quickly
- Instances were replaced automatically

This design favors fail-fast recovery
over serving slow or unreliable responses.

## Tradeoffs and Limitations

Fail-fast health checks are not universally correct.

Replacing instances does not resolve failures
caused by shared downstream dependencies
such as databases or external APIs.

Aggressive health checks can also lead to:
- Instance churn
- Restart loops
- Amplified outages if misconfigured

Health check design is a tradeoff between:
- Availability
- Performance
- Stability

## Key Takeaways

- Health checks are contracts, not facts
- Whatever is defined as "healthy" determines system behavior
- Crashes trigger recovery; silent failures do not
- Load balancers control traffic, not instance lifecycle
- Auto Scaling must be explicitly connected to application health
- Resilience is a design decision, not a default feature

