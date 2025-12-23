# Day 2 – Instance Failure Simulation

This stage focuses on a loud and obvious failure:
the sudden termination of a running EC2 application instance.

The goal is to observe how the system reacts
when an instance disappears unexpectedly.


## Failure Scenario

One EC2 instance managed by the Auto Scaling Group
was manually terminated while the application was serving traffic.

No configuration changes were made before the termination.
The system was observed in its baseline state.


## Expected Behavior

Based on the architecture design, the expected behavior was:

- The remaining healthy instance should continue serving traffic.
- The Auto Scaling Group should detect the reduced capacity
  and launch a replacement instance.
- The new instance should join the target group once healthy.


## Observed Behavior

- The Application Load Balancer continued serving requests
  without visible downtime.
- Traffic was routed exclusively to the remaining healthy instance.
- No errors were observed from the user perspective.
- The Auto Scaling Group detected the missing instance
  and launched a replacement automatically.
- Once initialized, the new instance became healthy
  and rejoined the target group.


  ## Key Insight

Instance failures are loud failures.
They are easy for the platform to detect and recover from.

Auto Scaling Groups combined with Load Balancers
are effective at maintaining availability
when individual compute instances fail.



## Limitation of This Failure Model

This experiment demonstrates recovery from instance-level failures only.

It does not address:
- Application-level bugs
- Performance degradation
- Downstream dependency failures

These scenarios are more dangerous because
the system may appear healthy while users suffer.

While instance failures are common,
they are not the most dangerous failure mode in real systems.

The more critical failures are silent failures,
where applications degrade without triggering automated recovery.

These scenarios are explored in the next stage of the project.


