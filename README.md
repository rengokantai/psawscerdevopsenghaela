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
