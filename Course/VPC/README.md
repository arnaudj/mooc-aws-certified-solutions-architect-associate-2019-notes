# CHAPTER 7 | VPC
<!-- TOC -->

- CHAPTER 7 | VPC
  - [VPC](https://aws.amazon.com/vpc/)
  - Terminology
    - Amazon VPC components
    - Network
  - Default VPC
  - VPC Peering
  - How to VPC Peering
  - Build Your Own Custom VPC
    - NAT instances exam tips
    - NAT gateways exam tips
  - [Network Access Control Lists vs Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html)
  - Custom VPC's and ELB's
  - [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
  - [VPC Enpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)

<!-- /TOC -->
## [VPC](https://aws.amazon.com/vpc/)

From [User guide](https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/what-is-amazon-vpc.html):

A virtual private cloud (VPC) is a virtual network dedicated to your AWS account. You can launch your AWS resources, such as Amazon EC2 instances, into your VPC. 

Notes:
* CIDR: Classless Inter-Domain Routing
* Network Access Control List (ACLs) are stateless (security groups are stateful)
* A subnet is deemed to be a Public Subnet if it has a Route Table that directs traffic to the Internet Gateway.
* Transitive peering is unsupported (with A-B-C, then A-C not possible, needs A-B, A-C, etc)

Exam tips:
* 1 AZ can have several subnets, but 1 subnet belongs to 1 AZ
* When you create a VPC, you get 
  * defaults: Route Table + Network Access Control (NACL) + Security group
  * not created: no subnet, no internet gateway
* Amazon reserves 5 IPs / subnet
* 1 VPC = 1 internet gateway max
* Security groups can't span VPCs


## Terminology
### Amazon VPC components

* A Virtual Private Cloud: A logically isolated virtual network in the AWS cloud. You define a VPC’s IP address space from ranges you select.
* Subnet: range of IP addresses in your VPC, where you can place groups of isolated resources.
* Internet Gateway: give resources access to&from the internet.
* NAT Gateway: an aws managed NAT service (more availability&bandwidth, less admin). Instances remain hidden behind the NAT.
* NAT instance: regular instance that you configure to provide NATing (e.g, from aws community AMIs)
* Virtual private gateway: The Amazon VPC side of a VPN connection.
* Peering Connection: A peering connection enables you to route traffic via private IP addresses between two peered VPCs.
* VPC Endpoints: Enables private connectivity to services hosted in AWS, from within your VPC without using an Internet Gateway, VPN, Network Address Translation (NAT) devices, or firewall proxies.
* Egress-only Internet Gateway: A stateful gateway to provide egress only access for **IPv6** traffic from the VPC to the Internet.

![VPC_Diagram](https://docs.aws.amazon.com/vpc/latest/userguide/images/default-vpc-diagram.png)

### Network
* public subnet: a subnet that has internet traffic routed through AWS's Internet Gateway. Any instance within a public subnet can have a public IP assigned to it (e.g. an EC2 instance with "associate public ip address" enabled).
* private instances: instances on a private subnet
* private subnet means the instances are not publicly accessible from the internet. They do NOT have a public IP address. For example, you cannot access them directly via SSH. Instances on private subnets may still access the internet themselves though (i.e. by using a NAT Gateway).


## Default VPC

* Amazon provides a default VPC to immediately deploy instances.
* All Subnets in default VPC have a route out to the internet.

## VPC Peering

* You can peer one VPC to another VPC using private IP subnets.
* You can peer VPC's with others AWS accounts as well as with other VPC's in the same account.

## How to VPC Peering

* Overlapping CIDR Blocks is not supported: You can't connect two VPC's that have the same CIDR.
* Transitive Peering is not supported:

    You have a VPC peering connection between VPC A and VPC B (pcx-aaaabbbb), and between VPC A and VPC C (pcx-aaaacccc). There is no VPC peering connection between VPC B and VPC C. You cannot route packets directly from VPC B to VPC C through VPC A.

    ![transitive-peering](https://docs.aws.amazon.com/vpc/latest/peering/images/transitive-peering-diagram.png)

## Build Your Own Custom VPC

* [VPC and Subnet Sizing](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Subnets.html#vpc-subnet-basics) The first four IP addresses and the last IP address in each subnet CIDR block are not available for you to use, and cannot be assigned to an instance.

* For Nat Instances you have to disable the [Source/Destination Checks](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html#EIP_Disable_SrcDestCheck).
* Use [Nat Gateways](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html#nat-gateway-basics) instead of Nat Instances

    ![nat-gateway](https://docs.aws.amazon.com/vpc/latest/userguide/images/nat-gateway-diagram.png)

### NAT instances exam tips
* Disable [Source/Destination Checks](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_NAT_Instance.html#EIP_Disable_SrcDestCheck).
* NAT instances must be on a public subnet
* NAT instances are behind a security group
* Private instances must have a route to reach the NAT instance
* High availability can be achieved via autoscaling groups, multiple subnets in different AZs, and a failover script

### NAT gateways exam tips
* Redundant within an AZ
* 5Gbps to 45Gbps
* Managed (patching)
* Not linked to a security group; no need to disable Source/Destination Checks
* Auto public IP assignment

## [Network Access Control Lists vs Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html)

![vpc&NACL](https://docs.aws.amazon.com/vpc/latest/userguide/images/security-diagram.png)

* Default Network ACL (NACL): is allow all inbound and outbound
* New custom NACL defaults is deny all inbound and outbound
* A NACL as separate rules for inbound and outbound and not stateful (response subject to outbound rules & vice versa)
* 1 NACL belongs to a single VPC
* You can block IPs via NACLs
* 1 NACL can be associated with multiple subnets ([NACL Basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html?shortFooter=true))
* A subnet can only have 1 NACL at a time (by default, the default NACL)
* Rules are applied in ascending numerical order, so when you should create the first rule having number 100 and add others on incremental of 100
* NACL are **stateless** (opposite of Security Groups)
* Remember to open [ephemaral ports](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports) on your outbound rules only.
* If you have to block specific IP addresses, use network ACL not Security Groups

## Custom VPC's and ELB's

* Design tip: Remember that ELB needs at least two public AZ's, so when designing your network, remember to create at least two public subnets in two AZ.

## [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

VPC Flow Logs is a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC. Flow log data can be published to Amazon CloudWatch Logs and Amazon S3. After you've created a flow log, you can retrieve and view its data in the chosen destination.

## [VPC Enpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection.