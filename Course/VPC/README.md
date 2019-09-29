# CHAPTER 7 | VPC
<!-- TOC -->

- CHAPTER 7 | VPC
  - [VPC](https://aws.amazon.com/vpc/)
    - Region default VPC specifications
  - Creating a new VPC:
  - Terminology
    - Amazon VPC components
    - Network
  - VPC Peering
    - How to VPC Peering
  - Build Your Own Custom VPC
    - NAT instances exam tips
    - NAT gateways exam tips
  - [Network Access Control Lists vs Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Security.html)
  - Custom VPC's and ELB's
  - [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)
  - [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)
  - References

<!-- /TOC -->
## [VPC](https://aws.amazon.com/vpc/)

A virtual private cloud (VPC) is a virtual network dedicated to your AWS account.

Notes:
* CIDR: Classless Inter-Domain Routing
* NACLs: Network Access Control List (ACLs) are stateless (security groups are stateful)
* A Route Table controls where network traffic is directed (traffic towards `destination` CIDR is directed to `target` AWS element)
  * Every Route Table contains a `Local route` for communication within the VPC over IPv4 (destination=vpcCIDRhere to target='local')
  * It can enable internet access via a route destination='0.0.0.0/0' to target='igw-xyzabcd' (igw: Internet Gateway)
  * Route selected: the most specific one matching (ex: /32 chosen before /24)
* Transitive peering is unsupported (with A-B-C, then A-C not possible, needs A-B, A-C, etc)

Topology:
* A VPC is within a region
* A VPC subnet is within an AZ
  * A subnet has 1 route table and 1 NACL
* 1 AZ can have several subnets
* A VPC has 0/1 internet gateway 
* An internet gateway is dedicated to a single VPC
* A Security group belongs to 1 VPC
* When you create a VPC, you get 
  * defaults: Route Table + Network Access Control (NACL) + Security group
  * not created: no subnet, no internet gateway
* Amazon reserves 5 IPs / subnet

### Region default VPC specifications
* Configured with a CIDR /16 (172.31.0.0/16)
* A subnet /20 in each AZ, allocating a public IP by default
* All Subnets linked to the Main Route Table
* Attached Internet Gateway - main route table sends all traffic to the IG, via a 0.0.0.0/0 route => all subnets are public with internet access
* NACL default: allow all in&out
* Security group default: allow all out, in from instances with the same security group

## Creating a new VPC:
* When you create a subnet, you specify the CIDR block for the subnet
* Each subnet must be associated with a route table, which specifies the allowed routes for outbound traffic leaving the subnet. Every subnet that you create is automatically associated with the main route table for the VPC.
* New NACL default: deny all in&out
* New security group has: allow all out, deny all in

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


*Default VPC: 1 subnet per AZ:*

![VPC_Diagram](https://docs.aws.amazon.com/vpc/latest/userguide/images/default-vpc-diagram.png)

### Network
* public subnet: a subnet whose Route Table has a route that directs traffic to the Internet Gateway (and is configured to allocate public IPs for instances)
* public instance: instance on a public subnet, with a public IP address associated
* private instance: instance on a private subnet
* private subnet means the instances are not publicly accessible from the internet. They do NOT have a public IP address. For example, you cannot access them directly via SSH. Instances on private subnets may still access the internet themselves though (i.e. by using a NAT Gateway).


## VPC Peering

* You can peer one VPC to another VPC using private IP subnets.
* You can peer VPCs with others AWS accounts as well as with other VPCs in the same account.

### How to VPC Peering

* In VPC A's Route Table, add a destination='vpcB'sCIDR' and target=pcx-xyz, and vice versa in VPC B
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

* A NACL as separate rules for inbound and outbound and not stateful (response subject to outbound rules & vice versa)
* 1 NACL belongs to a single VPC
* You can block IPs via NACLs
* 1 NACL can be associated with multiple subnets ([NACL Basics](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html?shortFooter=true))
* A subnet can only have 1 NACL at a time (by default, the default NACL)
* Rules are applied in ascending numerical order, so when you should create the first rule having number 100 and add others on incremental of 100
* Each network ACL includes a default rule whose rule number is an asterisk. This rule ensures that if a packet doesn't match any of the other rules, it's denied.
* NACL are **stateless** (opposite of Security Groups)
* Remember to open [ephemaral ports](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-network-acls.html#nacl-ephemeral-ports) on your outbound rules only.
* If you have to block specific IP addresses, use network ACL not Security Groups

## Custom VPC's and ELB's

* Design tip: Remember that ELB needs at least two public AZ's, so when designing your network, remember to create at least two public subnets in two AZ.

## [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html)

* Capture information about the IP traffic going to and from network interfaces in your VPC
* Ability to choose to log accept/deny or both
* Can log to a Cloudwatch or S3
* Some traffic won't be logged (traffic to default VPC router, domain resolution via default DNS, 169.254.169.254 instance metadata, etc)

## [VPC Endpoints](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints.html)

A VPC endpoint enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection.

Example use case: private instance that doesn't need internet access, but needs access to S3.

Tip: when using the CLI tools, you need to specify the region you use: `aws s3 ls --region us-east-2`


## References
* [User guide](https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/what-is-amazon-vpc.html)
* [Scenarios](https://docs.aws.amazon.com/en_pv/vpc/latest/userguide/VPC_Scenarios.html)
