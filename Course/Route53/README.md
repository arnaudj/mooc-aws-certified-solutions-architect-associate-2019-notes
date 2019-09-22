# CHAPTER 5 | Route53
<!-- TOC -->

- CHAPTER 5 | Route53
  - DNS IN AWS
    - [Alias record](https://support.dnsimple.com/articles/alias-record/)
    - [Routing policies available in AWS](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)
    - Exam tips
  - DNS
    - [What's DNS](https://www.cloudflare.com/learning/dns/what-is-dns/)
    - [IPv4 vs IPv6](https://www.guru99.com/difference-ipv4-vs-ipv6.html)
    - [Top Level Domains:](https://en.wikipedia.org/wiki/Top-level_domain)
    - [SOA Record](https://en.wikipedia.org/wiki/SOA_record)
    - [NS Record](https://www.cloudflare.com/learning/dns/dns-records/dns-ns-record/)
    - [A Record](https://www.cloudflare.com/learning/dns/dns-records/dns-a-record/)
    - [TTL, Time to Live](https://en.wikipedia.org/wiki/Time_to_live#DNS_records)
    - [CNAME Record](https://support.dnsimple.com/articles/cname-record/)

<!-- /TOC -->

## DNS IN AWS

### [Alias record](https://support.dnsimple.com/articles/alias-record/)
Alias records are CNAME records on steroids:
* are AWS specific
* can only redirect queries to selected AWS resources (ELB, S3, cloudfront, etc)
* can be placed at apex level (ex: example.com)
* dynamically resolve to 1/N type A records (as if config was static)

Cf [Choosing Between Alias and Non-Alias Records](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/resource-record-sets-choosing-alias-non-alias.html )

### [Routing policies available in AWS](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html)

* Simple routing policy: 
  * Given 1 record (with 1..N values (IPs)): returns the N IPs in random order
* Multivalue answer routing policy:
  * Same as simple routing policy, with per-record healthchecks
  * Given 1..8 records with 1 value (IP): returns the 1..8 IPs passing their own healthcheck in random order
* Weighted routing policy:
  * Set weight per item (percentage-based routing)
  * Health check is possible on individual record set. If failed, record set will be removed from Route53 until back on again.
* Latency routing policy:
  * Resolves to the resource that has the best latency from the user's location.
* Failover routing policy:
  * Resolve the secondary in case primary falls
  * Requires to create a health check before
* Geolocation routing policy:
  * Resolves to a resource based on the user's location (ex: all european clients to 1 instance, for business/regulation purpose)
* Geoproximity routing policy:
  * Resolves based on the geo location of the users and your resources.
  * Has concept of bias to shrink/expand the source geo region
  * Based on Route53 traffic flow.

### Exam tips
* ELBs are to be resolved via DNS: they don't have a pre-defined IP address
* Always choose Alias over CNAME

## DNS

_You won't be questioned in the exam about the next sections (up to ALIAS record).
It's just for your knowledge and very good for better understanding the course._

### [What's DNS](https://www.cloudflare.com/learning/dns/what-is-dns/)

The Domain Name Systems (DNS) is the phonebook of the Internet. Humans access information online through domain names, like nytimes.com or espn.com. Web browsers interact through Internet Protocol (IP) addresses. DNS translates domain names to IP addresses so browsers can load Internet resources.

### [IPv4 vs IPv6](https://www.guru99.com/difference-ipv4-vs-ipv6.html)

* IPv4 is a 32-Bit IP Address.
* IPv6 is 128 Bit IP Address and was created to fulfil the need for more Internet addresses.

### [Top Level Domains:](https://en.wikipedia.org/wiki/Top-level_domain)

A top-level domain is one of the domains at the highest level in the hierarchical Domain Name System of the Internet (```.com``` ```.net``` ```.org``` as an example).

In the case of ```.co.uk```, ```.co``` is the second level domain and ```.uk``` is the top level domain

### [SOA Record](https://en.wikipedia.org/wiki/SOA_record)

A Start of Authority record (abbreviated as SOA record) is a type of resource record in the Domain Name System (DNS) containing administrative information about the zone, especially regarding zone transfers.

Example of a SOA record

```bash
$ dig SOA +multiline google.com

[...]
;; ANSWER SECTION:
google.com.        55 IN SOA ns1.google.com. dns-admin.google.com. (
                238640061  ; serial
                900        ; refresh (15 minutes)
                900        ; retry (15 minutes)
                1800       ; expire (30 minutes)
                60         ; minimum (1 minute)
                )
[...]
```

### [NS Record](https://www.cloudflare.com/learning/dns/dns-records/dns-ns-record/)

NS stands for 'name server' and this record indicates which DNS server is authoritative for that domain (which server contains the actual DNS records)

```bash
$ dig NS google.com

[...]

;; ANSWER SECTION:
google.com.        2073    IN    NS    ns4.google.com.
google.com.        2073    IN    NS    ns1.google.com.
google.com.        2073    IN    NS    ns2.google.com.
google.com.        2073    IN    NS    ns3.google.com.

[...]
```

### [A Record](https://www.cloudflare.com/learning/dns/dns-records/dns-a-record/)

The ‘A’ stands for ‘address’ and this is the most fundamental type of DNS record, it indicates the IP address of a given domain

```bash
$ dig A google.com

[...]

;; ANSWER SECTION:
google.com.        62    IN    A    216.58.201.14

[...]
```

### [TTL, Time to Live](https://en.wikipedia.org/wiki/Time_to_live#DNS_records)

When a caching (recursive) nameserver queries the authoritative nameserver for a resource record, it will cache that record for the time (in seconds) specified by the TTL.

### [CNAME Record](https://support.dnsimple.com/articles/cname-record/)

CNAME records can be used to alias one name to another. CNAME stands for Canonical Name.
CNAME can't be used on the root domain. (This is a contractual limitation imposed by the RFC 1912 and RFC 2181, not a technical one.)


A common example is when you have both example.com and www.example.com pointing to the same application and hosted by the same server.

```bash
$ dig www.dnsimple.com

[...]

;; ANSWER SECTION:
www.dnsimple.com.    3595    IN    CNAME    dnsimple.com.
dnsimple.com.        55    IN    A    104.245.210.170

[...]
```