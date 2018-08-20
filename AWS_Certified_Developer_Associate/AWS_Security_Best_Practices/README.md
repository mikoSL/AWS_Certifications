# AWS Security Best Practices(Aug 2016)

![AWS_Security_BestPractice_MindMap](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/AWS_Security_BestPractice_MindMap.png)

## AWS Shared Responsible Model
* Comparing with three different services
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

## AWS Account, IAM Users, Groups and Roles

## Secure Data (At Rest and In Transit)

### Protect data at Rest
* Threat to Data At Rest
![ThreattoDataAtRest](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/4_ThreattoDataAtRest.JPG)

#### S3
![S3FeaturesForProtectDataAtRest_commented](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/5_S3FeaturesForProtectDataAtRest_commented.jpg)
#### EBS
* EBS volume in raw, unformatted mode.
* Can partition EBS volume.
* Attach EBS volumes to EC2 instances.
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

#### RDS
* Same secure infra as EC2.
* If needed, use app-level or at platform layer using SQL cryptographic functions.
* MySQL cryptographic functions, like:
```
INSERT INTO Customers (CustomerFirstName, CustomerLastName)
VALUES (AES_ENCRYPT('John', @key), AES_ENCRYPT('Smith',@key));

```
* NOTE: SQL range queries are no longer applicable to encrypted portion of data.
* Not return "John" or "Jonathan" if the content of CustomerFirstName is encrypted at app or platform layer.
```
SELECT CustomerFirstName, CustomerLastName from Customers
Where CustomerName LIKE 'Jo%'

```
* Still return exactly match "John"
```
SELECT CustomerFirstName, CustomerLastName from Customers
Where CustomerFirstName = AES_ENCRYPT('John', @key)

```
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

#### Glacier
* Protected using server-side encryption.
* AWS generates seperated unique encryption keys for each Glacier archive and encrypts it using AES-256.

#### DynamoDB
* BEST practice: use raw binary fields or Base64-encoded string fields!

#### EMR
* BY default, EMR instance does not encrypt data at rest!
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

### Protect data in Transit
* Threat to data in transit
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

#### S3
* SSL/TLS

#### RDS
* SSL/TLS provides peer authentication via server X.509 certifications.
* Single self-signed certificate associated with MySQL or MicrosoftSWL listener. This provides peer identity authentication and prevents man-in-the-middle or identity-spoofing attacks in the server side.
* Oracal: AES or Triple DES.

#### DynamoDB
* SSL/TLS(HTTPs)

#### EMR
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

## Secure your OS and Applications

* Disable root API access keys and secret keys.
* Restrict access to instances from limited IP range using Security Groups.
* Password protect the .pem file on user machines.
* Delete keys from the authorized_keys file on your instances when someone leaves or no longer need accessing them.
* Rotate credentials(DB,Access keys)
* Regularly run least privilege checks using IAM user Access Advisor and IAM user Last Used Access Keys.
* Use bastion hosts to enforce control and visibility.

### Creating custom AMIs
* Cleanup task before publish AMI
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

* Secure Linux/Unix AMIs
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

* Secure Windows AMIs
![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

### Bootstrapping
* Puppet, Chef, Capistrano, Cloud-Init or Cfn-Init
* Actions:
1. Security sw update install the latest patches, service packs and critical updates beyond the patch level of AMI.
2. Initial application patches install application level updates, beyond the current application level build as captured in AMI.
3. Contextual data and configuration enables instances to apply configurations specific to the environment in which they are being launched-production, test, or DMZ/internal, for example.
4. Register instances with remote security monitoring and management system.

### Managing patches

### Controlling security for public AMIs

### Protecting your system from Malware

### Mitigating compromise and abuse

### Using additional application security practices
* Change vendor-supplied defaluts before creating new AMIs.
* Remove or disable unnecessary user accounts.
* Implement a single primary function per EC2 instance to keep functions that require different security levels from co-existing on the same server. e.g. web server, database server and DNS on sperate servers.
* Enable ONLY necessary and secure services, protocols, deamons. Diable all non-essential services.
* Disable or remove all unnecessary functionality, such as scripts, drivers, features, subsystem, EBS volumes and web servers.
* Transfer files with SSH not insecure protocol e.g. FTP

## Secure your infrastructure

### Using VPC
* VPC-IPSec
* VPC-AWS Direct Connect

### Using security zoning and network segmentation
* BEST Practice: Segment infrastructure into zones that impose similar security controls.
* Methods:
1. VPC
2. Security groups to manage access to instances. (stateful --> enable firewall rules in BOTH directions)
3. NACLs(Network Access Control List) stateless management of IP traffic.Work with Security group and can allow or deny traffic EVEN BEFORE it reaches security group.
4. Host-based firewalls to control access to each instance.
5. Create a threat protection layer in traffic flow and enforcing all traffic to traverse the zone.
6. Access control at other layers.(e.g. app and services.)

### Strengthening network security
* Always use security group.
* Augment security group with NACLs.
* Use IPSec or AWS Direct Connect for trusted connections to other sites.
* Protect data in transit.
* For large-scale development, design network security in layers.
* VPC flow logs (further visibility as it enables your to capture information about IP traffic)

### Securing periphery systems: user repositories(IAM users), DNS, NTP

![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

### Building threat protection layers
* VPC
* implicit firewall rules at hypervisor-layer
* NACLs
* security groups
* host-based firewall
* IDS/IPS system
* Inline threat protection technologies examples:
1. 3rd-party firewall devices in EC2 instances (soft blades)
2. UTM (Unified Threat Management) gateway
3. Intrusion prevention system
4. Data loss management gateways
5. Anomaly detection gateway
6. Advanced persistent threat detection gateways

![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

### Test security

### Managing metrics and improvement

![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

### Mitigating and protecting against Dos & DDoS attacks

![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

## Manage security monitoring, altering, audit trail and incident response

![SharedResponsibleModel](https://github.com/mikoSL/AWS_Certifications/blob/master/AWS_Certified_Developer_Associate/AWS_Security_Best_Practices/pic/0_SharedResponsibleModel.png)

### Using Change Management tools

### Managing Logs for critical transactions

### Protecting log information

### Logging faults
