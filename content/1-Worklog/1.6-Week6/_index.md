---
title: "Week 6 Worklog"
date: 2026-05-25
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---



### Week 6 Objectives:

* Master resource provisioning, querying, and automation via the AWS Command Line Interface (AWS CLI) using named profiles and JMESPath expressions.
* Understand NoSQL database design principles, partition key strategies, and indexing mechanisms in Amazon DynamoDB.
* Implement high-performance in-memory caching using Amazon ElastiCache (Redis) to offload database read pressure and achieve sub-millisecond query latency.

### Tasks to be carried out this week:
| Day | Task | Start Date | Completion Date | Reference Material |
| --- | --- | --- | --- | --- |
| 2   | - Studied AWS CLI operational paradigms, named profiles (`~/.aws/credentials`), output formatting (`json`, `table`, `text`), and output filtering using JMESPath (`--query`) | 25/05/2026 | 25/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - **Hands-on AWS CLI:** Developed Bash scripts utilizing AWS CLI to query EC2 instance states, audit VPC subnets, and parse JSON payloads programmatically | 26/05/2026 | 26/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Researched Amazon DynamoDB NoSQL fundamentals: Partition Keys (PK), Sort Keys (SK), Global/Local Secondary Indexes (GSI/LSI), and On-Demand vs. Provisioned Capacity modes | 27/05/2026 | 27/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Studied Amazon ElastiCache in-memory caching engines (Redis vs. Memcached) and caching strategies: Lazy Loading (Cache-Aside) vs. Write-Through | 28/05/2026 | 28/05/2026 | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Hands-on DynamoDB & ElastiCache:** Provisioned a DynamoDB table, deployed an ElastiCache Redis cluster inside a private subnet, and configured application-level caching to accelerate database reads | 29/05/2026 | 29/05/2026 | <https://cloudjourney.awsstudygroup.com/> |


### Week 6 Insights & Reflections:

* **Programmatic Infrastructure Management via AWS CLI & JMESPath Querying:**
  * **Beyond the Management Console:** Transitioning from clicking on the AWS Web Console to executing CLI commands is the fundamental step toward DevOps automation and Infrastructure as Code (IaC).
  * **Precision Filtering with JMESPath (`--query`):** API responses from AWS CLI often return massive JSON structures. Leveraging native JMESPath expressions directly inside CLI calls (e.g., `--query "Reservations[*].Instances[*].{ID:InstanceId,State:State.Name}"`) enables precise data extraction directly in terminal shell scripts without relying on heavy external utilities like `jq`.

* **NoSQL Data Modeling Mechanics in Amazon DynamoDB:**
  * **Avoiding Hot Partitions:** Unlike relational databases where normalization and SQL joins are standard, DynamoDB partitions data physically across storage nodes based on a hash of the Partition Key (PK). A well-designed Partition Key must exhibit high cardinality (e.g., `user_id` or `device_uuid`) to distribute Read/Write Capacity Units (RCU/WCU) uniformly and prevent "hot partition" bottlenecks.
  * **Secondary Indexes (GSI vs. LSI):** Local Secondary Indexes (LSI) share the same Partition Key as the base table but use a different Sort Key and must be defined at table creation time. Global Secondary Indexes (GSI) can use completely different Partition and Sort Keys, can be created on-demand anytime, and maintain their own provisioned throughput.

* **Caching Architecture with ElastiCache (Redis) & Lazy Loading Strategy:**
  * **Offloading Database Read Hotspots:** Primary databases (RDS or DynamoDB) are designed for persistent storage, making disk I/O a potential bottleneck under extreme read traffic. Placing an ElastiCache in-memory cluster in front of the database serves popular queries in sub-millisecond response times.
  * **Lazy Loading (Cache-Aside) Pattern:** The application first queries ElastiCache. On a *cache hit*, data is returned instantly. On a *cache miss*, the application fetches data from the primary database, populates ElastiCache with a defined Time-To-Live (TTL), and returns the payload. Setting appropriate TTLs prevents stale data accumulation while ensuring self-healing memory management.

* **Engine Selection: Amazon ElastiCache Redis vs. Memcached:**
  * **Memcached:** Ideal for simple, multi-threaded key-value caching where data structures are simple strings or HTML fragments and advanced persistence or replication is not required.
  * **Redis:** Essential when requiring complex data structures (Hashes, Lists, Sets, Geospatial), in-memory data persistence (Snapshots/AOF), Multi-AZ replication with automatic failover, and high-availability enterprise clustering.