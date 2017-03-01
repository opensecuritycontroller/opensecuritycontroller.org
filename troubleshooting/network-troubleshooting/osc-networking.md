#Troubleshoot OSC Network Issues  
This procedure explains how to troubleshoot failure in connecting to OSC using OSC command line or Web Client. If the OSC IP address is not configured according to the local networking requirements network issues might occur.  
## Accessing OSC Console  
Access OSC console when trouble shooting network issues.OSC console access in VMWare and Openstack environments are shown below.
###VMWare Deployments  
For OSC installed on VMware, access the ESXi host by using the native vSphere desktop application or the vSphere web client. 
Right click on the Virtual machine and select `Open Console`.
###OpenStack Deployments  
For OSC installation on OpenStack go to the `Instances` page on OpenStack Horizon, select `OSC instance` and select `Console`.  

##IP Configuration Check
At the login screen on the console enter the credentials. For credentials contact OSC system administrator.  
After login successfully, use the CLI prompt to check and configure the network using following IP parameters:  

***OSC IP Address and Netmask***  
Check IP configuration on the OSC management port:   [show network ip](../../references/cli.md/#show-network-ip)  
If it is not an expected IP address or netmask change ip-address  [set network ip](../../references/cli.md/#set-network-ip)  

***Gateway Address***  
Check network route to view the default gateway ip  [show network route](../../references/cli.md/#show-network-route)  
If it is not expected gateway address change gateway address [set network gateway](../../references/cli.md/#set-network-gateway)  

***Ping remote server or gateway***  
After verifying network configurations are correct, ping a remote IP address to test network connectivity by using the command  
**Command**: `C>ping REMOTE_IP_ADDRESS`
