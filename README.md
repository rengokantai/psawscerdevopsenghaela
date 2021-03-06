## psawscerdevopsenghaela
###1
####4

| stateless applications  |stateful applications   |
|---|---|
|stores no session information   |stores state information locally   | 
|no knowledge of previous user interactions required   |may require knowledge of previous user interactions   | 
|user session info typically stored in a database   | possible to scale horizontally with session affinity  |  
|can easily scale horizontally   | not easy to scale horizontally  |  

scale up vertically: first must stop instance,then instance settings->change instance type

###3 Understanding Auto Scaling
####11
create a dns host zone in route 53=godaddy domain name. copy NS name to godaddy, create certificate in certificate  manager.  
create load balancer(classic load balancer, set security group=443,add 2 subnets. Step 4: Configure Health Check->ping protocol=TCP(not default, HTTP)

####12 Setting up a Launch Configuration and Auto Scaling Group

[script](https://gist.github.com/mikepfeiffer)
```
yum install httpd -y
/sbin/chkconfig --levels 235 httpd on
service httpd start
instanceId=$(curl http://169.254.169.254/latest/meta-data/instance-id)
region=$(curl http://169.254.169.254/latest/dynamic/instance-identity/document | grep region | awk -F\" '{print $4}')
echo "<h1>$instanceId</h1>" > /var/www/html/index.html
aws ec2 create-tags --resources "$instanceId" --tags Key=Name,Value="PROD-$instanceId" --region "$region"
```
5:40
Health check grace period: The length of time that auto scaling waits before checking an instance's health status.  
The grace period begins when an instance comes into service  

default cooldown: The number of seconds after a scaling activity completes before another can begin.  

simple scaling policy vs (default, step-based) policy)  
simple scaling policy's activity must obey cooldown time

install load testing tool artillery:
```
npm install -g artillery
```
in host machine, send large request to target machine:
```
artillery quick --duration 1200 --rate 20 https://target
```
####24 Getting started with DynamoDB
02:40 ->single attr primary key  
03:20-> double attr primary key  
If pk is consists of 1 attr, then it is partition key  
If is consists of 2 attr, then is partiion key and sort key  
partitions are seperate chunks of storage

####25 Provisioned throughput
Evetually consistent reads->response might not reflect the results of a recently completed write operation  
Strongly consistent reads->Response reflecting the updates from all prior write operations that were successful  
1 strongly consistent read of 4kb=1 read capacity unit
2 eventually consistent read of 4kb=1 read capacity unit  
one 1kb write= 1 write capacity unit  

Quiz:  
100 strongly consistent reads/sec for items 2.5kb in size.->2.5/4=0.625=1(read capacity unit per item)* 100 reads/sec=100 read capacity units


####26 Secondary indexes
local secondary index: Has the same partition key as the table but a different sort key, can only be created when you create the table  
global secondary index: An index with a partition key and a sort key that can be different from those on the table  

####27 Creating DynamoDB Tables
scan->return all items  
query->Partition key=, Sort key may <,=, or>  

create global second index: indexes tab->create index

###5
####31Setting up a virtual
04:00  
For a net instance, set sg(NAT):  (actions->networking->change sg)
SSH->22  
All traffic->from VPC, such as 10.0.0.0/16  


05:30
create 2nd pri/pub subnet,this time we use NAT gateway instead NAT instance  
subnet->public subnet, create new EIP

####32 Creating a database tier for the app
create sg(web):
http->10.0.0.24 (first public subnet)
http->10.0.2.24(second public subnet)  


create (rds) sg:
mysql/aurora->source=from previous sg(web)
mysql/aurora->source=from previous sg(NAT)  


02:00  
create a new DB subnet group:  
az=a, subnetid=2 priv subnets (add one by one)  

02:47  
go to intances->mysql->multiaz dep->step4 configure advanced settings:  
public accessible:no  
vpc sg: choose (RDS)


####33 Defing the launch config
02:40 S3 policy  
```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":["s3:ListBucket"],
      "Resource":["arn:aws:s3:::bktname"]
    },{
       "Effect":"Allow",
      "Action":["s3:GetBucket"],
      "Resource":["arn:aws:s3:::bktname/*"]
    }
  ]
}
```
then attach AmazonEC2RoleforSSM, 
script
```
#!/bin/bash

region=$(curl http://169.254.169.254/latest/dynamic/instance-identity/document | grep region | awk -F\" '{print $4}')

yum update -y
yum install -y httpd24 php56 php56-mysqlnd

service httpd start

cd /home/ec2-user/
aws s3 cp s3://bktname/app.zip . --region "$region"
unzip app.zip -d /var/www/html/

curl https://amazon-ssm-us-west-2.s3.amazonaws.com/latest/linux_amd64/amazon-ssm-agent.rpm -o amazon-ssm-agent.rpm
yum install -y amazon-ssm-agent.rpm
```

####34 Setting up a load balancer and auto scaling group
01:25 create elb->add two public subnets
02:25 create auto scaling group subnet->add two private subnets

####35Validating the deployment
Go to RDS, get the address, then connect:  
in NAT instance,check
```
nslookup myapp.xxx.us-west-2.rds.amazonaws.com
```
