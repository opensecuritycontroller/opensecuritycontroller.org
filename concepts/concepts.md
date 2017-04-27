# OSC Concepts

To successfully use OSC, it is important to first understand its main concepts. The OSC main concepts help define its architecture and use cases and are built-upon related industry concepts such as security groups, policies, etc.  

## Virtualization Connectors
This concept defines the information needed to connect OSC to a virtualization provider - such as OpenStack - and to an SDN controller, for instance, IP addresses and user credentials. The OSC platform supports multiple virtualization connectors enabling the user to orchestrate security appliances on multiple virtualization platforms.  

## Security Groups
Security groups are used to manage virtual assets and instances such as tenant networks, subnets, and individual virtual machines.  Once a security group is defined, it can be bound to a deployed security service function to protect the grouped assets.  

## Manager Connectors
This concept defines the information needed to connect OSC to security service managers, for instance, IP addresses and user credentials or API keys. The OSC platform supports multiple manager connectors enabling the user to simultaneously orchestrate security appliances provided by different service managers.  

## Policies
A security policy's role is to define and enforce what assets need to be protected and how those assets are protected. OSC retrieves the policies defined by a security manager by utilizing the manager connector and binds them to a security group along with a security service for enforcement.  

## Security Service Functions
Security services such as IPS or next-generation firewall are intended to be deployed to protect the assets contained in a security group. As defined by OSC, a security service function describes the software image file as well as other metadata, such as the function's model and version. Some supported operations include managing service function versions and upgrading or downgrading the software image for deployed appliances.  

## Distributed Appliances
A distributed appliance associates the managed security services to the virtualization environments needing protection. A distributed appliance is defined by the security service function, one or more virtualization connectors, and a single manager connector. Each relationship between the domains defined by the appliance's manager connector and each virtualization connector represents a [virtual system](/concepts/concepts.md#virtual-security-systems).  

![Distributed Appliance](images/distributed_appliance.png)  
*Distributed Appliance and Virtual Systems*  

## Deployment Specifications
A deployment specification enables the user to deploy security services on the virtualization platform. A deployment specification is defined through a distributed appliance and allows the user to specify the deployment target and the number of instances to be deployed. Deployment targets can consist of a group of hosts or entire network tenants.  

## Virtual Security Systems
A virtual security system (virtual system) is the logical container object for all distributed appliances. This concept refers to the relationship between a single virtualization connector and a domain defined by the manager connector associated with the distributed appliance.  

## Traffic Policy Mappings
Traffic policy mapping represents the relationship between a security policy and a virtual security system. A mapping is created when a user binds a security group to a virtual system providing a security policy.  

## Appliance Instances
An appliance instance is a single deployment unit of the distributed appliance. Each instance represents a deployed security function which will intercept the traffic between the protected virtual machines.  

## Jobs and Tasks
Some of the actions that are performed in OSC trigger jobs and tasks that perform asynchronous operations against external services such as security managers and the virtualization platform. The set of synchronized operations defines a job while its underlying actions are referred to as tasks. The overall status of the job depends on the status of its tasks.
* Example: Synchronizing a distributed appliance is a job while checking the connectivity to the security manager is a task.  

## Alarms, Alerts and Archives
**Alarms** are setup to trigger alerts. Alarms can be set for different types of failures such as job failures, system failures, or appliance instance failures. Different severity levels can be defined for each type of failure.  

**Alerts** are generated for every failure event that occurs. Alerts are generated with different severity levels depending on the severity level defined in the alarm.  

A user can **archive** older jobs and alerts. Tasks related to the archived jobs are also archived. A schedule can be set to automatically archive the jobs and alerts or can be manually triggered to archive whenever required.  

## Plugins
Plugins facilitate the communication between OSC and the security manager and SDN controllers. There are two types of plugins:
* **[SDN Controller Plugins](/plugins/sdn_controller_plugin.md)** allow OSC to communicate with the SDN controllers.
* **[Manager Plugins](/plugins/security_mgr_plugin.md)** allow OSC to communicate with the security managers.  
