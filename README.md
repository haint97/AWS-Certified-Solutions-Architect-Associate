# AWS-Certified-Solutions-Architect-Associate

# EC2
- Models:
    + On-demand:  Pay a fixed rate by the hour/second with no commitment
    + Reserved: Provide with a capacity reservation, and offer a significant discount on the hourly charge for an instance. Contract terms are 1-3 years
    + Spot: Bid whatever price we want for instance capacity, providing for even greater savings if applications have flexible start and end times
If the spot instance is terminated by EC2, will not be charged for a partial hour of usage. If you termiate the instance yourself, you will be charged for any hour in which the instance ran
	+ Dedicated Hosts: Physical EC2 server dedicated for your use. Can help reduce costs by allowing you to use your existing server-bound software licenses
- EBS: Virtual hard disk drive in the cloud
	+ Termination Protection is turned off by default
	+ On an EBS-backed instance, default action for the root EBS volume to be deleted when the instance is terminated
	+ EBS root volumes of default AMI's CAN be encrypted 
	Additional volumes also can be encrypted
- Security Groups
	+ All inbound traffic is blocked by default
	+ All outbound traffic is allowed
	+ Changes to Security Group take effect immediately
	+ Can have any number of EC2 instances within  a security group
	+ Can have multiple security groups attched to EC2 instances
	+ Are STATEFUL
	+ If we create an inbound rule allowing traffic in, that traffic is automatically allowed back out again
	+CAN NOT block specific IP addresses using SG, instead of use Network ACL
	+ Specify allow rules but not deny rules
	+ EBS Types
- EBS snapshot:
	+ Volumes exist on EBS
	+ Snapshots exist on S3. Think of snapshots as a photograph of the disk
	Are point on time copies of Volumes
	Are incremental - means that only the blocks have changes since your last snapshot are moved to S3
	+ The first snapshot, it may take some time to create. Then it will only replicate if take a second snapshot
	+ To create a snapshot for EBS Volumes that serve as a root devices, should stop the instance before taking the snapshot (optional)
	+ Can create AMI's from both Volumes and Snapshots
	+ Can change EBS volume sizes on the fly, incluing changing the size and storage type
	+ Volumes will ALWAYS be in the same AZ as the EC2 instance
	+ Can not have an EC2 in on AZ and then and EBS Volume in another .
- Migrating EBS
	+ To move and EC2 volume from one AZ to another, take a snapshot of it, create and AMI from the snapshot and then use the AMI to luanch the EC2 instance in a new AZ
	+ To move and EC2 volume from on region to another:  take a snapshot of it => create an AMI from the snapshot => copy the AMI from one region o the other => use the copied AMI to launch the new EC2 instance in the new region
- EBS Encryption
	+ Snapshot of encrypted volumes are encrypted autmatically
	+ Volumes resotred from encrypted snapshots are encrypted automatically
	+ Can share snapshots but only if they are unencrypted
	+ Can be shared with other AWS accounts or made public
	+ An unencrypted root device volume that needs to be encrypted: create snapshot of the unencryped root device volume => create a copy of the snapshot and select the encrypt option => create AMI from encrypted snapshot => use that MI to launch new encrypted intances
- EBS vs Instance Store
	+ Instance store volume are somtimes called Ephemeral stotage
	+ ISV can not be stopped. If the underlyinghost failes => lose data
	+ EBS backed instances can be stopped. Not lose the data on this instance if it is stopped
	+ Can reboot both, not lose data
	+ By default, both ROOT volumes will be deleted on termination. However, with EBS volumes, we can tell AWS to keep root device volume
- Encrypting root device volume
	+ Create a snapshot => create a copy of the snapshot => select encrypt option => create an AMI from encrypted snapshot => use AMI to launch new encrypted instances
- Enhance networking
	+ ENI: basic networking
	+ Enhanced netowrk: 10gbps - 100
	+ Elastic Fabric Adaptor: High performance computing, OS bypass
- Cloudwatch:
	+ Monitoring performance
	+ With EC2, CW will monitor events every 5 minutes by default
	+ Can create CW alarms which trigger notifications
	+ CW is all about performance. Cloutrail is all about auditing
- Role: 
	+ more secure than storing acesskey and secret key on individual EC2 instances
	+ Easier to manage
	+ Can be assigned to an EC2 instance after it is created
	+ Are universal -  use in any region
- EFS:
	+Only pay for the storage that use (no pre-provisioning required)
	+ Can be scale up to Petabytes
	+ Support thousands of concurrent NFS connections
	+ Data is stored accoss multiple AZ's with a region
	+ Read after write consistency 
- Placement groups
	+ Cluster: Low network latency / high network throughput. Can not span multiple AZ
	+ Spread:  Individual critical instances
	+ Partitioned: Multiple EC2 instances HDFS, HBase, Cassandra
	Tips:  
		Cluster: Can not span multiple AZ. 
		Spread and partitioned can
		Name specify for a replacement group must be unique in AWS account
		Only certain type of instances can be launched in a placement group
		Can't merge PG
# RDS
 - Tips:
 	+ RDS runs on virtual machines
	+ Can not login to these OS
	+ RDS is not serverless
- Back ups:
	+ Automated:
		+ Recover database to any point in time with a "retention period" (1-35 days)
		+ Take full daily snapshot and wil also store transactions throughout the day
		+ When do a recovery. Choose the most recent daily back up => apply transaction logs relevant to that day  => allows to do a point in time recovery down to a second, within the retention period
		+ Enable by default
		+ Backup data is store in S3.  Get free storage equal to the size of database. 
		+ Backups are taken within a defined window. During backup window, storage I/O may be suspended while data is beging backed up and may experienece elevated latency
	+ Database snapshots:
		+ Done by manually
		+ Stored even after delete the original RDS instance unlike automated backups
- Multi-AZ
	+ Allows to have an exact copy of production db in another AZ.
	+ AWS handles replication, so when db is written to, this write will automatically be synchronized to the stand by db
	+ In the event of planned db maintenance/ db instance failure/ AZ failure RDS will automatically failover to thr standy so that db operations can resume quickly without administrative intervention
	+ Be used for disaster recovery only. Not primarily used for improving performance. For performance improvement, we need Read Replicas
	+ Available for: SQLServer / MySQL/ Orcacle/ PostgreSQL/ MariaDB. Aurora by its own architecture is  comletely fault tolerant
- Read replicas
	+ Used for scaling not for DT
	+ Must have automatic backups turned on in order to deploy a read replica
	+ Have up to 5 read replica copies of any db
	+ Can have read replicas of read replicas (latency)
	+ Each read replica will have its own DNS end point
	+ Can have read replicas that have multi-az
	+ Can create read replicas of multi-az source db
	+ Can have a read replica in second region
	+ Read replicas can be promoted to be theri own db. This breaks the replication
- DynamoDB
	+ Stored on SSD 
	+ Spread accross 3 geographically distinct data centres
	+ Eventual Consistent Reads (default)
	+ Strongly Consistent Read
	+ Accelerator:
		+ Fully managed, highly available, in-memory cache
		+ 10x performance improvement
		+ Reduces request time from milliseconds to microsecibds - even under load
		+ No need for developers to manage caching login
		+ Compatible with Dynamodb API calls
	+ On-demand capacity:
		+ Per per request pricing
		+ Balance cost and performance
		+ No minimum capacity
		+ No charge for read/write - only storage and backups
		+ Pay more per request than with provisioned capacity
		+ Used for new product launches
	+ On-Demand Backup and Restore:
		+ Ful backups at any time
		+ Zero impact on table performance or availability
		+ Consistent within seconds and retained until deleted
		+ Operates within same region as the source table
		+ Point in time recovery:
			+ Protects against accidental writes or deletes
			+ Restore any point in the last 35 days
			+ Incremental backup
			+ Not enable by default
			+ Lastest restorable: 5 minutes in the past
	+ Streams:
		+ Time-ordered sequence of item-level changes in a table
		+ Stored 24 hour
		+ Insert /update/delete
		+ Combine with lambda funciton for functionality like stored procedures
	+ Global tables:
		+ Globally distributed apps
		+ based on dyna,odb streams
		+ multi-region redundancy for DR and HA
		+ No app rewrites
		+ Replication latency under 1  second
