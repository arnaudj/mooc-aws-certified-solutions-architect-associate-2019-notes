# High availability
<!-- TOC -->

- High availability
  - Elastic Load Balancers
    - [What's an Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/)
    - [Types of loadbalancers](https://aws.amazon.com/elasticloadbalancing/features/#Details_for_Elastic_Load_Balancing_Products)
    - Load Balancers - Lab
    - Advanced Load Balancers theory

<!-- /TOC -->
## Elastic Load Balancers

### [What's an Elastic Load Balancer](https://aws.amazon.com/elasticloadbalancing/)

Elastic Load Balancing automatically distributes incoming application traffic across multiple targets, such as Amazon EC2 instances, and others.

### [Types of loadbalancers](https://aws.amazon.com/elasticloadbalancing/features/#Details_for_Elastic_Load_Balancing_Products)

* Application Load balancers: Best for load balancing HTTP & HTTPS traffic. They operate at layer 7.
* Network Load Balancer: Loadbalancing TCP traffic where extreme performance is needed. They operate at layer 4. (expensive price)
* Classic Load Balancer: Legacy ELB, mostly operate at layer 4, but can go up to 7. (affordable price)

* X-Forwarded-For: originating IP address of a client
* HTTP 504 error: gateway has timed out: check your application health and/or scale it

### Load Balancers - Lab

* You must set up health checks in order to let the load balancer to understand if the instances are up and running

Exam tips:
* Load balancers are exposed via DNS name, never by IP
* Read ELB FAQ for Classic Load Balancers

### Advanced Load Balancers theory
Exam tips:
* Sticky sessions to stick to the same instance (ex: if local disk access needed)
* Cross Zone load balancing: load balancing across AZs
* Path patterns: load balancing based on URL path 