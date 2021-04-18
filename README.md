# AWS-Certified-Solutions-Architect-Associate

#EC2
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
