# CHAPTER 6 | Databases
<!-- TOC -->

- CHAPTER 6 | Databases
  - Databases 101
    - [OLTP vs. OLAP](https://www.datawarehouse4u.info/OLTP-vs-OLAP.html)
    - [AWS Databases](https://aws.amazon.com/products/databases/)
    - RDS
    - DynamoDB - No SQL
    - RedShift - OLAP
    - Elasticache - In Memeory Caching
    - RDS Wordpress Lab
    - [Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)
    - [RDS Multi-AZ](https://aws.amazon.com/rds/details/multi-az/)
    - [RDS Read Replicas](https://aws.amazon.com/rds/details/read-replicas/)
    - [DynamoDB](https://aws.amazon.com/dynamodb/)
    - [Redshift](https://aws.amazon.com/redshift/)
    - [Elasticache](https://aws.amazon.com/elasticache/)
    - [RDS Aurora](https://aws.amazon.com/rds/aurora/)

<!-- /TOC -->
## Databases 101

### [OLTP vs. OLAP](https://www.datawarehouse4u.info/OLTP-vs-OLAP.html)
* OLTP: online transaction processing 
* OLAP: online analytical processing

### [AWS Databases](https://aws.amazon.com/products/databases/)

### RDS
RDS: Relational Database Service

Provides access to:

* MySQL
* PostgreSQL
* Oracle
* Aurora
* MariaDB
* SQL Server

RDS specs:
* runs on VMs, it is not serverless (except Aurora Serverless)
* it is not possible to SSH into the host
* maintenance/upgrade of host is Amazon's responsibility

### DynamoDB - No SQL

### RedShift - OLAP

### Elasticache - In Memeory Caching
* Redis / memcached

### RDS Wordpress Lab
For the web server instance to have access to the database, add a security group inbound rule: 
* Go to EC2,oOpen the security group of the DB
* Add an entry for Mysql, with source being the security group of the web server
* Start a VM, test connectivity: `mysql -h database-1.....rds.amazonaws.com -u login -ppassword -D thedatabasename -e "SHOW TABLES"`


### [Automated Backups](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_WorkingWithAutomatedBackups.html)

There are two different types of backups for AWS:

* Automated: Are enabled by default, data is stored in S3 and are enabled automatically (free S3 storage of the size of the DB)
  * Are deleted as soon as the DB is deleted
* Database snapshots: triggered manually
  * Kept even if the DB is deleted

When you restore a backup or a snapshot, the restored version of the database will be in a new RDS instance, with a new DNS name

[Encryption](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html) You can encrypt your Amazon RDS DB instances and snapshots at rest by enabling the encryption option on your Amazon RDS DB instances.

### [RDS Multi-AZ](https://aws.amazon.com/rds/details/multi-az/)
For disaster recovery not for scaling.

Does auto failover to the standby upon DB maintenance, DB failure, AZ failure.

When you provision a Multi-AZ DB Instance, Amazon RDS automatically creates a primary DB Instance and synchronously replicates the data to a standby instance in a different Availability Zone


### [RDS Read Replicas](https://aws.amazon.com/rds/details/read-replicas/)

For read-heavy database workloads.

The read replica operates as a DB instance that allows only **read-only** connections

* For scaling not for disaster recovery
* Replica can be in another region
* Replica can be multi-AZ for failover
* You need to have automatic backups on in order to have a read replica.
* You can have up to 5 read replicas of any DB at the time of writing.
* Each replica has its own DNS name.
* Replicas be promoted to master (is then an independent DB)
* You can enable encryption on your replica even if your master is not.

### [DynamoDB](https://aws.amazon.com/dynamodb/)

Amazon DynamoDB is a key-value and document database that delivers single-digit millisecond performance at any scale. It's fully managed.

* Uses SSD storage.
* Spread across 3 distinct data centres.
* Eventual Consistent Reads (Default)
* Strongly Consistent Reads.
* It's scalable.
* DynamoDB can be very expensive for writes.

### [Redshift](https://aws.amazon.com/redshift/)

Amazon Redshift is a fast, scalable data warehouse that makes it simple and cost-effective to analyze all your data across your data warehouse.

Redshift stores data by [colums](https://en.wikipedia.org/wiki/Column-oriented_DBMS).  By storing data in columns rather than rows, the database can more precisely access the data it needs to answer a query rather than scanning and discarding unwanted data in rows. Query performance is increased for certain workloads.

Columnar data stores can be compressed much more than row-based data.

* Single node: You can start with a single node (Up to 160Gb of ram in one node)
* Multi-Node:
  * Leader node: Manages clients and distribute queries
  * Compute nodes: Store data and execute queries (up to 128 computer nodes max)

* Pricing:
  * You are charged for the total number of hours you run across all your compute nodes.
  * You are charged for backups
  * You are also charged for data transfer within a VPC

* Security:
  * Encrypted in transit using SSL
  * Encrypted at rest using AES-256 encryption
  * By default redshift takes care of key management.

* Availability:
  * Is available only in 1 AZ
  * Can restore the snapshot to new AZ's in the event of an outage.

* Backups
  * Enabled by default with 1 day retention period
  * Maximum retention of 35 days
  * Data copies: 3: 2 on computes nodes (original, replica), 1 on S3 (can be in another region, for DR)
  

### [Elasticache](https://aws.amazon.com/elasticache/)

Elasticache: Managed, Redis or Memcached-compatible in-memory data store. Basically, it's a DB that saves everything in memory to increases I/O performance.

* Types of Elasticache:
  * Memcached
  * Redis: In memory key-value store

### [RDS Aurora](https://aws.amazon.com/rds/aurora/)

Aurora is a closed source DB engine, driver-compatible with MySQL and PostgreSQL

Has better performance (tailored for their cloud hardware): 5x MySQL, 3x PostgreSQL

Amazon Aurora is fully managed by Amazon Relational Database Service (RDS), which automates time-consuming administration tasks like hardware provisioning, database setup, patching, and backups.

Amazon Aurora features a distributed, fault-tolerant, self-healing storage system.

It delivers high performance and availability with up to 15 low-latency read replicas, point-in-time recovery, continuous backup to Amazon S3, and replication across three Availability Zones (AZs).

* Scaling:
  * Auto scales in 10GB Increments up to 64TB
  * Compute resources can scale up to 32vCPUs and 244GB of Memory.

* Data copies:
  * 6 copies total: data is in 3 AZ, with 2 copies per AZ
  * Automated backups enabled by default
  * Snapshots (manual) can be shared with other AWS accounts
  * Handles the loss of up two copies of data without affecting database **write** capability.
  * Handles the loss of up three copies of data without affecting database **read** capability.

* Replicas:
  * Aurora Replicas: Separate aurora replicas (up to 15 replicas).
  * MySQL Read replicas: (up to 5 replicas).
  * Failover: Aurora replicas can be promoted to master