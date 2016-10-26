AUTOSCALING PROJECT FOR OCCOPUS
This is a project which aims to complement the autoscaling capabilities of Occopus. With these solution users can scale their application without user intervention in a predefined scaling range to guarantee that its running always with the optimum level of resources.

NODE TYPES
In the following I will describe, how can we deploy infrastructures by Occopus with auto scaling capabilities.
The architecture consists of 4 node types which are:
-	Load Balancers (lb) 
-	Consul (db)
-	Data Node (da)
-	Prometheus (prometheus)


In this infrastructure nodes are discovered by consul which is a DNS service discovery software and monitored by Prometheus, a monitoring software which support alert definitions which later will help you write custom scaling events. Incoming traffic from the user is go through the load balancers to the appropriate data node where your application should be run.

WARNING: Don’t forget to reimport node definition files by Occopus every time you modify them. 
Also make sure that you "auth_data" file have resouce to docker plugins. For this add the following lines to the auth_data file:
       -
type: docker
        
	auth_data: unused 

STEPS TO INSTALL THE PROJECT

1. First you have to start Occopus in the Rest-Service mode. This can be done by the following command:
# occopus-rest-service -h [your vm ip address]
For more information, visit: 
http://www.lpds.sztaki.hu/occo/user/html/api-user.html?highlight=rest#occopus-rest-service
2. You have to specify the connectors to the cloud(s) where you want to deploy your infrastructure in the node_definition file
More info: http://www.lpds.sztaki.hu/occo/user/html/createinfra.html?highlight=definition#node-definition
3. Decide in which intervals the infrastructure should scale the load balancers and the data nodes. You can find scaling ranges in your infrastructure descriptor file (infra_da.yaml). Keep in mind that Occopus has to start at least one nodes from each node type to work properly!
3. In the data node cloud init file (da_cloud_init.yaml) you have to add the actual application that you want to run. You can find a preinstalled Apache java applet on your data nodes. 
4. Autoscaling events (scale up, scale down) are based on Prometheus rules which acts as thresholds, let’s say scale up if cpu usage > 80%
These rules can be found in the prometheus_cloud_init.yaml file in the section of “Prometheus rules”. Here you can see the implementation of a cpu, ram, hdd utilization in your cluster with some threshold values however depending on your application you may need to adjust them.
5. The last thing you have to do is define the ip address where your Occopus installation is running. This can be done in the prometheus_cloud_init file in the section of “executor config” by simple modifying the two IP addresses.


USER DEFINED RULES

The infrastructure is monitored by Prometheus. It stores a lots of information about the nodes in your infrastructure. You can check them at:  https://{prometheusnodeIP}:9090/graph
You can write your own scaling rules by querying in Prometheus’s database and add these rules in the prometheus_cloud_init.yaml file in the section of “prometheus rules”. For testing and monitoring you can use the built in graph menu which is accessible at “https://prometheusnodeIP:9090/graph. For more information, visit: https://prometheus.io/docs/alerting/rules/
