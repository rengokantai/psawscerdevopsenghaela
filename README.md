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
