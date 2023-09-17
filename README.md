# AWS CheatSheet 2023

## AWS Certified Solutions Architect - Associate (SAA-CO3)

---
### AWS Fundamentals
 - Region - physical location , consists > 2 AZs
 - Availability Zones - redundant power, network and connectivity
 - Edge Locations - endpoints for caching content, CDN.

---
### Identity Access Management (IAM)
 - Secure Root Account
    - Enable MFA
    - Create admin group and use it for your users.
    - Create user accounts and add user
 - Type: 
    - User: Physical person
    - Groups: Functions, developer, admin, financial
    - Roles: Internal usage within AWS
 - Info
    1. Assign Permission using IAM policy documents JSON
    2. IAM is universal (no region)
    3. New user no permission assigned by default. 
    4. Use Access key id and secret to access AWS via API and command line. Only able to view once. 
    5. Always setup password rotations.
    6. Always assigned minimum privileges to user to do their job.
 - Federation:
    1. IAM Federation: combine existing user account with AWS - use same credential (7Microsoft AD) to login AWS.
    2. Identity Federation: use SAML standard / AD

```
Policy Documents 
{
    Version: xxx,
    Statement: [
        {
            Effect: “Allow”,
            Action: “*”,
            Resource: “*”
        }
    ]
}
```

---
### Simple Storage Service (S3)
 - info
    - Object based
    - Unlimited storage, 0bytes to 5TB
    - Universal namespace.
    - Buckets are private by default
    - For static content
    - Automatic scaling
    - Cannot disable versioning, only suspend
    - Can replicate objects from bucket to buckets
        - Delete markers are not replicated by default
 - Type: 
    - S3 standard 
    - S3 standard Infrequent access - backup, DR files
    - S3 one zone Infrequent access - non critical data
    - S3 Glacier Instant Retrieval - long term, few milliseconds retrieval
    - S3 Glacier Flexible Retrieval - Long term data, few hours or minutes
    - S3 Glacier Deep Archive - 12 hours retrieval - old financial records
    - S3 Intelligent-tiering - unpredictable access patterns
 - S3 object lock
    1. Write once and ready many
    2. Individual or bucket
    3. Governance mode - cannot overwrite / delete unless have special permission.
    4. compliance mode - cannot overwrite / delete including root user. 
    5. S3 Glacier vault lock - S3 glacier 
 - S3 Performance
    1. More prefix, better performance
    2. Multipart uploads
    3. S3 byte-range fetches to download files

---
### Elastic Compute Cloud (EC2)
 - Type: 
    1. On Demand - pay by hour
    2. Spot - purchase unused capacity at discount <=90%
    3. Reserved - reserve 1 - 3 years , 72% discount
    4. Dedicated - physical EC2 , compliance/licensing requirement
 - Info: 
    1. Attach and detach role on EC2 without stop or terminate.
    2. Security group take effect immediately
    3. All inbound traffic is blocked by default, outbound traffic is allow
    4. Bootstrap script run when instance start. 
    5. User Data = bootstrap script
    6. Metadata = data about EC2
 - Network type:
    1. Elastic Network Interface - basic networking
    2. Enhanced Networking - 10Gbps - 100Gbps
    3. Elastic File Access - Machine Learning / High Performance Computing
 - Placement Group:
    1. Cluster Placement Groups - low network latency, high network throughput, no AZ
    2. Spread Placement Groups - individual critical EC2, AZ
    3. Partition Placement Groups - Cassandra, Multiple EC2 instances, AZ
 - Extra:
    1. VMware - Extend private VMware cloud/Vcenter to AWS public cloud
    2. AWS outpost - extending AWS to own data center
        - AWS outpost rack for large deployment
        - AWS outpost servers for smaller deployment

---
### Elastic Block Storage EBS & Elastic File System EFS 
 - Type:
    1. General Purpose SSD GP2 - Boot Disk
    2. General Purpose SSD GP3 - high Performance app 
    3. Provisioned IOPS SSD IO1 - online transaction application, expensive
    4. Provisioned IOPS SSD IO2 - online transaction application
    5. Throughput Optimized HDD ST1 - big data, warehouse data, not boot volume
    6. Cold HDD SC1 - less frequent access data, not boot volume, lowest cost 
 - Info:
    1. Snapshot exist on S3 while volume exist on EBS.
    2. First snapshot take times, Snapshot can be share to other region but need to copy over
    3. EBS volume can be resize on the fly.
        - Can be stop and no lost data. 
    4. Instance store volume
        - Ephemeral volume
        - Cannot be stopped -> lost data
        - Can be reboot and not lose data. 
 - EFS:
    1. Support network file system V4 - High scalable shared storage 
    2. Read after write consistency
    3. Store multiple AZ
    4. Scale up to petabytes
    5. Amazon FSx for windows - windows use
    6. Amazon FSx for Lustre - high performance computing / financial modeling
 - Encrypted Volumes
    1. All data encrypted
    2. All snapshot are encrypted 
    3. All data in flight encrypted 
    4. All volumes created from snapshot are encrypted
 - How to create encrypted Volume:
    1. Create snapshot from unencrypted volume
    2. Copy snapshot with encrypted option
    3. Create AMI from encrypted snapshot
    4. Launch encrypted instance from AMI
 - EC2 Hibernation
    1. Faster bootup
    2. Instance EC2 less than 150GB
    3. Cannot hibernate more than 60 days

---
### Databases
 1. RDS - OLTP
 2. Redshift - OLAP, data warehouse
 3. Multi AZ
    1. Use for disaster recovery
    2. Automatic failover to standby instance
 4. Read Replica 
    1. Scaling, not Disaster Recovery
    2. Require automatic backup
    3. Up to 5 read replica
 5. Aurora
    1. 2 copies in each AZ, minimum 3 AZ
    2. Can share Aurora snapshot for other AWS account.
 6. DynamoDB
    1. Store in SSD storage
    2. Eventually consistency , strong consistency, transactional read (ACID). 
    3. Full backup anytime.
    4. DynamoDB Point In Time Recovery
        1. 5 minutes in the past
        2. Not enable by default
        3. Restore any point within 35 days
        4. Incremental backups
    5. Global Tables
        1. Global distributed applications
        2. DynamoDB stream
            - FIFO -> lambda
 7. Amazon DocumentDB - MongoDB
 8. AWS Keyspaces - Cassandra
 9. Neptune - Graph Database
 10. AWS Quantum Ledger Database QLDB - Immutable database
 11. AWS Timestream - Time-series data for analysis

---
### Virtual Private Cloud (VPC)
1. VPC
    - Route tables, network access control list, subnet, security groups
    - 1 subnets in 1 AZ
2. NAT Gateway
    - Redundant inside AZ
    - For internet access
    - For high availability with NAT Gateway
    - Create NAT gateway in each AZ
3. Security Groups
    - Stateful 
    - Allow include traffic to go out regardless outbounds rules. 
4. Network ACL
    - VPC comes with default network ACL
    - Custom Network ACL - deny all traffic until add rules
    - Stateless
    - Subnet associates
        - can associate with a network ACL. 
        - Default network ACL will be use
        - Associates multiple subnets. But one subnet only one network ACL.
    - Block IP address - not security groups 
5. Direct Connect
    - Connect your data center to AWS
    - High-throughput workloads
    - Stable and reliable secure connection
6. VPC Endpoints
    - Connect AWS services without leaving amazon internal network
    - Interface endpoints
    - Gateway endpoints
        - Support S3 and DynamoDb
7. VPC Peering 
    - Connect 1 VPC with another VPC via direct network route using private IP
    - Work as if same private network
    - Peer between region
    - AWS PrivateLink
        - peering > 10 VPCs
        - Network load balance on service VPC and ENI on customer VPC
8. AWS Transit Gateway
    - For IP multicast
9. VPN Hub
    - Securing network
    - Simplify network tropology
10. AWS Wavelength
    - 5G, mobile edge computing

---
### Route 53
1. Info:
    - Alias Record
        - Translate DNS to IP
    - Cname
        - Translate subdomain to another subdomain
    - Health Check
        - Can set health check on individual record
2. Type: 
    1. Simple Routing
        - Random choose
    2. Weighted Routing 
        - Based on weight value
    3. Latency-based Routing
        - Based on latency value
    4. Failover Routing
        - Based on Active and Passive
        - Switch between failure
    5. Geolocation Routing
        - Based on user location
    6. Geoproximity Routing 
        - AWS route user based on user location
    7. Multivalue Answer Routing
        - Based on health check and auto route user to active DNS

---
### Elastic Load Balancing (ELB)
1. Application load balance (Layer 7)
    1. Limitation - http and https only
    2. Have sticky session feature
        - But only target to target group level, not single EC2
2. Network load balance (Layer 4)
    - Use for extreme performance
3. Classic load balance (Layer 4/7)
    1. 504 means gateway timeout
    2. Need IPv4 of end user? X-forwarded-for in header
4. Extra:
    1. Sticky Session
        1. Allow user stick to same EC2 instance
        2. If EC2 removed from pool, load balance still direct user to that EC2, 
            - Solution: disable sticky session
    2. Deregistration Delay/ Connection Draining 
        1. Disable Deregistration Delay - 
            - load balancer close connection immediately when EC2 down
        2. Enable Deregistration Delay
            - Keep connection when EC2 down

---
### Monitoring 
1. CloudWatch
    - Default: monitor every 5 minutes
    - Place for logs
    - Can tie to other AWS service based on alarm
2. CloudWatch Logs insights
    - For SQL 
3. Kinesis
    - Real time logs
4. Grafana
    - Visualization of container / IOT metric
5. Amazon Managed Service for Prometheus
    - Kubernetes based metric mornitoring at scale. 
    - AWS EKS cluster or self managed cluster

---
### High Availability and Scaling
1. Auto Scaling Group - Auto scaling only on EC2
2. Put everything in AMI to reduce build time
3. Spread out ASG to multiple AZ
4. Elastic load balance - enable health check at load balance 
5. Scaling Database
    - Prefer horizontal scaling
        - Read replica 

---
### Decoupling Workflows
1. SQS 
    1. duplicate messages.
    2. Configure visibility timeout
    3. Not bi-directional
    4. Persist 14days
    5. Order queue - FIFO SQS
2. SNS
    1. Push notification
    2. Cloud watch alarm can use SNS to push notification
    3. API gateway use for external communications to SNS
3. AWS Batch
    1. Long running task > 15 minutes
    2. Batched workload
    3. Queued batch workloads 
    4. Alternative to AWS lambda due to runtime requirements
4. AmazonMQ
    1. RabbitMQ / ActiveMQ
    2. Managed messaging broker
5. AWS StepFunctions (like state machines)
    1. Serverless orchestration service
    2. Condition check - logic during workflows. 
    3. Failure catches,
    4. Wait period for long long time. 
6. AWS AppFlow
    1. SaaS data integration
        - Simplify data ingestion for Salesforce / Other third party app
    2. Bi-directional 

---
### Big Data
1. Redshift
    - Relational DB
    - Only support single AZ deployment
2. Elastic MapReduce
    - Made up of EC2 instances
    - Extract —> transform —> load
    - Basically run open source tools in EMR. 
3. Kinesis 
    - Real time solution to processing data
    - Can store data up to one year
    - Can be queue
4. AWS Athena
    - Serverless SQL 
    - Query data stored in S3
5. AWS Glue
    - Serverless ETL
    - Create schema for data when paired with Athena. 
6. AWS QuickSight
    - Tool to visualize data
7. AWS OpenSearch / AWS ElasticSearch
    - Analyzing cloud watch log files/ other documents
8. AWS Data Pipeline
    1. Automated workflows for move and transformation data.
    2. Integrate with RDS, S3, dynamoDB, redshift.
    3. Managed ETL
    4. Data driven and task-dependent ETL workloads
    5. Use cases:
        1. Copy data to redshift
        2. Copy css between S3 buckets
        3. Export RDS data to S3
        4. Import / export dynamoDB
9. Amazon Managed Streaming for Apache Kafka (MSK)
    1. Handle control plane operation
    2. User manage data plane operation

---
### Serverless Architecture
1. Lambda
    - Need to attach role to lambda
    - Triggered by S3, event bridge, kinesis
    - 10GB Ram and 15minutes of runtime
2. Container
    1. Kubernetes
        - Amazon EKS or EKS Anywhere (for on-premises)
    2. Fargate cannot work alone. 
        - Must use Amazon ECS / EKS
    3. Start with docker file —> build image —> upload to repo —> run on host
    4. Amazon ECR - store container image 
3. Amazon Aurora Serverless
    1. On-demand or auto scaling database
    2. Unknown workloads or traffic spikes for DB
    3. Capacity planning
4. Amazon Xray
    1. Gain application insight using request and responses 
    2. Trace and downstream response times
    3. Integrates natively in AWS Lambda & AWS API Gateway insights
5. Amazon AppSync
    1. Managed Graphql for any frontend apps
    2. Connect multiple DB and datapoint into single merged API.

---
### Security
1. AWS CloudTrail
    - Log all API calls of AWS account and store log in S3
    - Incident investigation
    - Near real-time intrusion detection
    - Industry and regulatory compliance
2. AWS Shield
    - Protect against DDos on layer 3 and 4 
    - Free but advanced cost money
3. AWS WAF
    - Allow / block requests except the one specific.
    - Count requests that match properties you specify
    - Operate at layer 7
        - SQL injections / cross site scripting
        - Block country / IP address
4. AWS Firewall Manager
    - Secure centrally for multiple AWS accounts and resources
5. AWS GuardDuty
    - Use AI to learn normal behavior and alert if there is abnormal behavior. 
    - Update DB of known malicious domains
    - Monitor CloudTrail logs , VPC logs
6. AWS Macie
    - Use AI to analyze data in S3 about PII , PHI, financial data. 
    - Good for HIPAA and GDPR compliance , prevent identity theft.
    - Automate remediation actions using AWS resources such as StepFunction. 
7. AWS Inspector
    - Perform vulnerability scan
    - Two types 
        1. Host assessment - for EC2 , need to install agent
        2. Network assessment - for VPC, no need install agent
8. AWS KMS - key management service
    - Create and control the encryption keys
    - Automatic key rotation
9. CLoudHSM
    - Dedicated HSM to user
    - No auomatic key rotation
    - Full control of underlying hardware
10. Secret Manager
    - Store password, confidential value. 
    - Support key rotation
11. Parameter Store
    - Store password
    - Not key rotation support
    - Not able to store more than 10,000 parameters
12. Pre-signed URL
    - Share private files from S3 bucket
13. AWS certificate Manager - generate SSL certificates
14. AWS Audit Manager - generate HIPAA or GDPR compliance report
15. AWS Artifact - a place to get audit and compliance report 
16. Cognito - for authentication service
17. Detective - analyze root cause of an event
18. AWS Network Firewall
    - filter network traffic before reach internet gateway
19. AWS Security Hub
    - View all security alerts across AWS services and AWS accounts

---
### Automation
1. CloudFormation Template
    - Immutable template to provision EC2 or AWS services
    - Never hardcode, use mapping and parameter store.
2. Elastic Beanstalk
    - Simple solution to bundle all related AWS services and deploy application
    - Like PaaS
3. System Manager
    - Automate administration patch, update
    - Automation document - RunBooks
    - Parameter Store
    - Control on-premises architectures
    - Able to do remote login on any host. 
    - Have to install system manager agent on host. 

---
### Caching
1. CloudFront - CDN
    - Only option to have https for a static website hosted in S3
    - Cache content at edge location.
2. Global Accelerator
    - IP address caching
    - Give user 2 global IP address
3. Database Caching
    1. In-memory database / ElasticCache (relational DB)
        1. Redis 
            - Multi AZ
            - Backup supported
        2. Memcached
            - No multi AZ
            - Backup not supported.
    2. DynamoDB Accelerator DAX (non-relational DB)

---
### Governance
1. Service Control Policy (SCPs)
    - Only method to restrict root account action
2. CloudTrail 
    - Centralized logs into one single AWS account
3. Isolating workload - have dev account, test account and prod account
4. AWS Config
    - Standardize / create rule for account, check for compliance
    - Automate remediate problems using automation documents/ lambda
    - See architecture history of changes
5. Authentication
    1. For internal user - AWS SSO / AWS Identity Center
    2. External user - Cognito 
    3. Microsoft
        - use AWS managed Microsoft AD
        - On premise - AWS AD connector
    4. Cross account role access > create more IAM credential 
6. Cost Management 
    1. Cost Explorer / AWS Cost and Usage Report 
    2. AWS Budgets
    3. AWS Compute Optimizer - generate recommendation for compute need
7. AWS Trusted Advisor
    - Free but paid version better
    - Auditing tool to generate reports, no provide solution
    - Can automate response to solve problem via eventbridget to trigger lambda
8. AWS Control Tower
    - Implement compliance and account governance within multi account 
    - Preventive / detective guardrails - implemented via SCPs and AWS Config
9. AWS License Manager
    - Manage license from supported vendors to prevent license abuse & overcharge. 
    - AWS env or on-premies
10. AWS Service Catalog
    - Provision pre-approved products and services via shared catalog portfolio
    - Written in CloudFormation template. 
11. AWS Proton
    - Provision entire application stack for container / serverless architectures.
12. Well Architecture Tools
    - Document architectural decision and measurement against industry best practises
13. AWS Health
    - Dashboard and service to provide notification to public or within AWS

---
### Migration
1. SnowBall
    - Slow internet / no internet 
    - type: 
        - Snowcone - 8TB
        - Snowball Edge - 48TB - 80TB
        - Snowmobile - 100PB
    - Shift to AWS and also AWS shift to you
2. Storage Gateway
    1. Hybrid cloud storage - merge on-premiese with cloud
    2. Run locally as VM on-premiese
    3. Type 
        1. File Gateway - NFS - keep local copy of files
        2. Volume Gateway - backup for migration
        3. Tape Gateway
3. DataSync 
    - Agent-based solution to migrate on-premiese storage to AWS
    - One time migration
4. TransferFamily
    - Move files using Secure File Transfer Protocol (SFTP) , FTP
5. AWS Migration Hub 
    - Track progress of application migration to AWS and also planning migration effort
    - Type 
        1. Database Migration Services
            1. Migrate oracle database —> AWS Schema Conversion tool —> Amazon Aurora DB
            2. Migrate mysql database —> AWS DMS —> Amazon Aurora DB
        2. Server Migration Services
            1. Migrate VMs —> AMI —> EC2 instance
            2. Install agent
6. Application Discovery Service
    1. agent - Install agent in each servers / VMs.
    2. Agentless - Agentless collector , easy VMs migration
7. Application Migration Service AWS MGN
    - Automate lift and shift entire application architecture to AWS 


---
### Frontend Web and Mobile
1. Amplify
    1. Simplified frontend web and mobile development.
    2. Full stack application hosted on AWS.
2. Device Farm
    1. Automated / manual testing for android, iOS, web 
3. Pinpoint
    - Allow engage with customer
    - For marketing team, business
    - Can target group of users.

---
### Machine Learning
1. Amazon Comprehend
    - Speed to text
    - Sentiment analysis
2. Amazon Kendra
    - Using ML for search service using unstructured text
3. Amazon Textract
    - Convert document (handwriting) to text.
4. Amazon Forecast
    - Make prediction based on time-series data.
5. Amazon fraud detection service
    - Determine fraud detection based on data
6. Amazon Transcribe
    - Convert video or audio to text
7. Amazon Lex
    - Chatbot
8. Polly
    - Convert text to natural speech
9. Amazon Rekognition
    - Content moderation using AI/ML
    - Image recognition
10. Amazon SageMaker
    - Train ML model and deploy
    - SageMaker NEO - able to train, optimize and run on specific CPU/GPU
    - Reduce running cost - use Elastic inference feature
11. Amazon Translate
    - Translate one language to another. 

---
### Media
1. Amazon Elastic Transcoder
    - Convert media files to optimized format
2. Amazon Kinesis Video Stream
    - Real time video streaming
