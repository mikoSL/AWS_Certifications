# Architecting for the Cloud(AWS Best Practices)(Feb 2016)

![Architecting for the Cloud AWS Best Practices MindMap](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/Design_principles_of_Architecting_Cloud.png)

## 1.Scalability
### 1.1 Stateless application
![How to distribute load to multiple nodes](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/1_HowToDistributeLoadToMultipleNodes.png)

### 1.2 Stateless component
* e.g HTTP cookies. DynamoDB is good choice to store native sessions.
* S3 and EFS for larger files.
* SWF**(Simple Workflow Service) store execution history.
![How to implement session affinity](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/2_HowToImplementSessionAffinity.png)

### 1.3 Distributed processing
![How to implement Distributed processing](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/3_HowToImplementDistributedProcessing.png)

## 2. Disposable resource instead of fixed

### 2.1 Instantiating Compute resource
#### 2.1.1 Bootstrapping
![Bootstrapping in Practices](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/4_BootstrappingInPractice.png)

#### 2.1.2 Golden image (snapshot of a particular state of resource)
* ECS instance
1. customize it then save its config by creating an AMI) (recommend using script)
2. having existing on-premising virtualized environment, then using VM Import/Export from AWS
3. prebaked shared AMI from AWS or 3rd parties.

* RDS database or EBS volumes (e.g. launching a new test environemnt by prepopulating database by instantiating it from RDS snapshot not importing from SQL script)

* Container(e.g.Docker)
![Container for golden image](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/5_DockerForDistrutedProcessing.png)

#### 2.1.3 Hybrid (Bootstrapping & golden image) e.g. Elastic Beanstalk is a hybrid model.
![Line between Bootstrapping and golden image](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/6_LineBetweenBootstrappingGoldenImage.png)

### 2.2 Infrastructure as Code
![CloudFormation](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/7_CloudFormation.png)

## 3. Automation
* Elastic Beanstalk
* EC2 auto recovery
* CloudWatch Alarm(alarm --> SNS --> lambda --> SQS or POST request to an HTTP/S endpoint)
* CloudWatch Event (stream --> Lambda or Kinesis or SNS topics)
* AutoScaling
* OpsWorks Lifecycel events (e.g.trigger chef recipes)
* Lambda scheduled events

## 4. Loose coupling (reduces interdependencies)
* Well-definded interfaces (components interact ONLY through specific, technology-agnostic interfaces e.g. RESTful APIs)
![API Gateway](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/8_APIGateway.png)

* Service discovery
![How to implement service discovery](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/9_HowToImplementServiceDiscovery.png)

* Asynchronous integration(SQS or Kinesis)
![Examples of asynchronous integration](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/10_ExamplesOfAsynchronousIntegration.png)

* Graceful failure
![Graceful failure in Practices](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/11_GracefulFailareInPractice.png)

## 5. Services, not servers
* Managed Services (SQS, S3, DynamoDB, CloudFront, ELB, CloudSearch, SES, Elastic Transcoder)
* Serverless architectures(Lambda, API Gateway, Cognito)

## 6. Database
### 6.1 Rational database (RDS)
* Scalability (Read Replica and data partitioning and sharding to scale write capacity)
![How to take advantage of read replica](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/12_HowToTakeAdvantageOfReadReplicas.png)

* High availability(RDS multi-AZ deployment feature, when primary node is failing, can have read-only mode by utilizing read replicas)
* Anti-patterns (use NoSQL if no need to join or complex transaction, use S3 for binary file e.g. image, video, audio)

### 6.2 NoSQL Database(DynamoDB)
* Scalability(DynamoDB partitioning and replication automatically)
* High Availability (DynamoDB synchronously replicate data accross three facilities in region)
* Anti-pattern(Use RDS if schema can not be denormalized or requires join)

### 6.3 Data Warehouse
* Scalability(massively parallel processing(MPP), columnar data storage and targeted data compression** encoding schemes)
* High availability(multi-nodes cluster automatically replicate to another node, continously backup to S3, monitor health of cluster and automatically re-replicate data from failed drives and replace node if needed)
* Anti-patterns(Redshift is a SQL DB, it is not for OLTP (Online Transaction Processing), not for concurrency workload involving reading and writing all columns for a small number of records at a time --use DynamaDB or RDS)

### 6.4 Search(CloudSearch-scala automatically, ElasticSearch- open source API )
* Scalability (CloudSearch partitioning and replication automatically)
* High availability(Both CloudSearch and ES store data across AZs)

## 7. Removing single points of failure
* Introducing redundancy(to remove single point of failure. 1,standby redundancy-failover normally used in RDS. 2, active redundancy, when one fail, other absorb its workload.)
* Detect failure (ELB, Route53, AutoScaling, OpsWorks, Elastic Beanstalk, deep health check)
* Durable data storage
1. Synchronous replication--strong consistency, having performance and availability issue, not recommend to maintain many synchronous replication
2. Asynchronous replication(decouple primary node from its replicas, used as a disaster recover solution)
3. Quorum-based replication--combine syn and asyn
![Data Durability in practice](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/13_DataDurabilityInPractice.png)
* Automated multi-data center resilience(protection over disruption, e.g AZs,ELB, autoscaling)
* Fault isolation and traditional horizontal scaling(handle harmful request)--Shuffle sharding (8 instances --> 4 shards with 2 instances each --> distribute customer to each shard-->customer try every endpoint until one succeeds)

## 8. Optimize for Cost
* Right size

![Continuous monitoring and tagging](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/14_ContinuousMonitoringAndTagging.png)

* Elasticity
1. AutoScaling
2. Services no capacity needed e.g.ELB, CloudFront, SQS, Kinesis firehose, Lambda, SES, CloudSearch
3. Easily modify capacity e.g. DynamaDB, RDS, ElasticSearch Service

* Take advantage of variety of purchasing options
1. Reserved capacity (ideal for predictable min requirement, Redshift, RDS, DynamaDB, CloudFront)
![Reserved Instance tips](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/15_ReservedInstanceTips.png)

2. Spot Instance (EC2, ideal for flexible start and end times)
![Spot pricing best Practices](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Cloud_Best_Practices/pic/16_SpotPricingBestPractice.png)

## 9. Caching
* Application data caching(ElasticCache: Memcached and Redis)
1. I/O-intensive database queries
2. outcome of computationally intensive processing

* Edge Caching (CloudFront)
1. Copies of static content(e.g. images, css files, streaming of pre-recorded video
2. Dynamic content(e.g. html response, live video)

## 10. Security
* Utilize AWS features for defense in depth (VPC, WAF, IAM)
* Offload security responsiblity to AWS (security patch repsonsiblity on AWS- RDS, ElasticCache, CloudSearch etc.)
* Reduce privileged access
* Security as code (CloudFormation)
* Real-time auditing(Config, Inspector, Trusted advisor, CloudWatch logs, CloudTrail, Lambda, EMR, ES)
