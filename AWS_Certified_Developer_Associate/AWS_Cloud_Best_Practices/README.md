# Architecting for the Cloud(AWS Best Practices)(Feb 2016)

![AWS_Cloud_Best_Practices_MindMap](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Well_Architected_Framework/pic/AWS_Well_Architected_Framework_MindMap.png)

## Scalability
### Stateless application
pic1 ![How to distribute load to multiple nodes]

### Stateless component
* e.g HTTP cookies. DynamoDB is good choice to store native sessions.
* S3 and EFS for larger files.
* SWF**(Simple Workflow Service) store execution history.
pic2 ![How to implement session affinity]

### Distributed processing
pic3 ![How to implement Distributed processing]

## Disposable resource instead of fixed

### Instantiating Compute resource
#### Bootstrapping
pic4 ![Bootstrapping in Practices]

#### Golden image (snapshot of a particular state of resource)
* ECS instance
1. customize it then save its config by creating an AMI) (recommend using script)
2. having existing on-premising virtualized environment, then using VM Import/Export from AWS
3. prebaked shared AMI from AWS or 3rd parties.

* RDS database or EBS volumes (e.g. launching a new test environemnt by prepopulating database by instantiating it from RDS snapshot not importing from SQL script)

* Container(e.g.Docker)
pic5 ![Container for golden image]

#### Hybrid (Bootstrapping & golden image) e.g. Elastic Beanstalk is a hybrid model.
pic6 ![Line between Bootstrapping and golden image]

### Infrastructure as Code
pic7 ![CloudFormation]

## Automation
* Elastic Beanstalk
* EC2 auto recovery
* CloudWatch Alarm(alarm --> SNS --> lambda --> SQS or POST request to an HTTP/S endpoint)
* CloudWatch Event (stream --> Lambda or Kinesis or SNS topics)
* AutoScaling
* OpsWorks Lifecycel events (e.g.trigger chef recipes)
* Lambda scheduled events

## Loose coupling (reduces interdependencies)
* Well-definded interfaces (components interact ONLY through specific, technology-agnostic interfaces e.g. RESTful APIs)
pic8 ![API Gateway]

* Service discovery
pic9 ![How to implement service discovery]

* Asynchronous integration(SQS or Kinesis)
pic10 ![Examples of asynchronous integration]

* Graceful failure
pic11 ![Graceful failure in Practices]

## Services, not servers
* Managed Services (SQS, S3, DynamoDB, CloudFront, ELB, CloudSearch, SES, Elastic Transcoder)
* Serverless architectures(Lambda, API Gateway, Cognito)

## Database
### Rational database (RDS)
* Scalability (Read Replica and data partitioning and sharding to scale write capacity)
pic12 ![How to take advantage of read replica]
* High availability(RDS multi-AZ deployment feature, when primary node is failing, can have read-only mode by utilizing read replicas)
* Anti-patterns (use NoSQL if no need to join or complex transaction, use S3 for binary file e.g. image, video, audio)

### NoSQL Database(DynamoDB)
* Scalability(DynamoDB partitioning and replication automatically)
* High Availability (DynamoDB synchronously replicate data accross three facilities in region)
* Anti-pattern(Use RDS if schema can not be denormalized or requires join)

### Data Warehouse
* Scalability(massively parallel processing(MPP), columnar data storage and targeted data compression** encoding schemes)
* High availability(multi-nodes cluster automatically replicate to another node, continously backup to S3, monitor health of cluster and automatically re-replicate data from failed drives and replace node if needed)
* Anti-patterns(Redshift is a SQL DB, it is not for OLTP (Online Transaction Processing), not for concurrency workload involving reading and writing all columns for a small number of records at a time --use DynamaDB or RDS)

### Search(CloudSearch-scala automatically, ElasticSearch- open source API )
* Scalability (CloudSearch partitioning and replication automatically)
* High availability(Both CloudSearch and ES store data across AZs)

## Removing single points of failure
* Introducing redundancy(to remove single point of failure. 1,standby redundancy-failover normally used in RDS. 2, active redundancy, when one fail, other absorb its workload.)
* Detect failure (ELB, Route53, AutoScaling, OpsWorks, Elastic Beanstalk, deep health check)
* Durable data storage
1. Synchronous replication--strong consistency, having performance and availability issue, not recommend to maintain many synchronous replication
2. Asynchronous replication(decouple primary node from its replicas, used as a disaster recover solution)
3. Quorum-based replication--combine syn and asyn
pic13 ![Data Durability in practice]
* Automated multi-data center resilience(protection over disruption, e.g AZs,ELB, autoscaling)
* Fault isolation and traditional horizontal scaling(handle harmful request)--Shuffle sharding (8 instances --> 4 shards with 2 instances each --> distribute customer to each shard-->customer try every endpoint until one succeeds)
## Optimize for Cost
* Right size
pic14 ![Continuous monitoring and tagging]

* Elasticity
1. AutoScaling
2. Services no capacity needed e.g.ELB, CloudFront, SQS, Kinesis firehose, Lambda, SES, CloudSearch
3. Easily modify capacity e.g. DynamaDB, RDS, ElasticSearch Service

* Take advantage of variety of purchasing options
1. Reserved capacity (ideal for predictable min requirement, Redshift, RDS, DynamaDB, CloudFront)
pic15 ![Reserved Instance tips]
2. Spot Instance (EC2, ideal for flexible start and end times)
pic16 ![Spot pricing best Practices]

## Caching
* Application data caching(ElasticCache: Memcached and Redis)
1. I/O-intensive database queries
2. outcome of computationally intensive processing

* Edge Caching (CloudFront)
1. Copies of static content(e.g. images, css files, streaming of pre-recorded video
2. Dynamic content(e.g. html response, live video)

## Security
