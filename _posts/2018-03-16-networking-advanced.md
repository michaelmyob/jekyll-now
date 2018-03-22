---
layout: default
title: "Networking Advanced"
---

## Formal Workshop: "Networking Advanced"

This workshop built upon our previous (fairly simple) workshop on networking. This time, we looked into the importance of knowing your subnet masks in order to communicate between Amazon AWS instances. This was run by JC and Prateek.

Key learnings:
- You can't deploy anything unless you have a VPC - an enclosed network where things internal can talk to each other
- A VPC is region specific
- By default, a VPC cannot talk to the rest of the internet. You need an Internet Gateway - IGW - (it’s a logical connection, no IP address). A gateway to the outside, interfaces with the outside world
- Availability zones - the physical location of the data centres. 3 in Sydney.
- Multi AZ = multi location = “High Availability”. Important to exist in multiple places for redundancy
- “Architecture is designed with failure in mind”. Failure is the normal scenario, and let’s plan for it
- Availability zones important because of subnets - you subdivide space in your VPC further in your zones. Subnets are tied to Availability zones
- eg. VPC 172.31.0.0/16
    - but for Availability zone A: we have 172.31.0.0/20,
    - for B we could have 172.31.16.0/20
    - Subnets cannot expand over AZ’s
    - VPC’s are bound to regions? CHECK
    - AZ’s are separate from each other. But they can communicate with each other. Subnets must be AZ specific.
- In AZ’s: Public and private separated subnets too. Things on the public subject will refer to a special object. “NACL”. Private subnets - NACL
- Routing Table
    - Same concept as on Mac locally
- AWS has routing table too. eg. 10.1.0.0/16. This is the target destination. Main target. Then the wildcard 0.0.0.0.
    - Route table entry is what determines if it’s public or private. Doesn’t matter it’s name.
- Subnet within the VPC. Hops directly between subnets in the VPC between AZ’s, but if it needs to then talk to the MYOB server, it goes via 0.0.0.0 externally. Don’t need to route via entire internet to talk to other subnet
- Security Group - Another list of things it allows to get in. Set of inbound/outbound rules.
- Ports. Services run on ports
    - Note: ephemeral ports - 49152 upwards. Temporary ports. Usually used to return info back to you.
- Sensible defaults
    - Apps should ALWAYS be on the private subnet.
- “ELB” Load balancer - if your app is sitting in the private subnet. You need to talk to it. Create a load balancer in the public subnet. Then that talks to private. They also have their own security groups. The load balancer will have a DNS. Can access that from anywhere.

***

[[Top]](#top)
