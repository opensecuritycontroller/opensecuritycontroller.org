# Troubleshoot OSC Network Issues  
This procedure explains how to troubleshoot failures when [accessing OSC](../../gettingstarted/accessing.md) using the OSC CLI or web application. If the OSC IP address is not configured according to the local networking requirements, network issues might occur.  
## Accessing OSC Console  
Access OSC console when troubleshooting network issues. OSC console access in VMware and OpenStack environments are shown below:

### VMware Deployments  
For OSC installed on VMware, access the ESXi host by using the native vSphere desktop application or the vSphere Web Client. 
Right-click on the virtual machine and select **Open Console**.

### OpenStack Deployments ###  
For OSC installation on OpenStack, go to the **Instances**` page on OpenStack Horizon, select the OSC instance to troubleshoot, and then select **Console**.  

## IP Configuration Check ##
Enter the credentials at the login screen on the console. Contact OSC system administrator for credentials.  
After a successful login, use the CLI prompt to check and configure the network using the following IP parameters:  

#### OSC IP Address and Netmask  
Check IP configuration on the OSC management port:   [`show network ip`](../../references/cli.md/#show-network-ip)  
If the expected IP address and netmask are not shown, configure the IP address:  [`set network ip`](../../references/cli.md/#set-network-ip)  

#### Gateway Address  
Check network route to view the default gateway IP address:  [`show network route`](../../references/cli.md/#show-network-route)  
If the expected gateway address is not shown, configure the gateway address: [`set network gateway`](../../references/cli.md/#set-network-gateway)  

#### Test Connection 
After verifying network configurations are correct, ping a remote IP address to test network connectivity by using the command:  
```
$ ping REMOTE_IP_ADDRESS
```
