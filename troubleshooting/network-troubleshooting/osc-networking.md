# Troubleshoot OSC Network Issues  
This procedure explains how to troubleshoot failures when [accessing OSC](/gettingstarted/accessing.md) using the OSC CLI or web application. If the OSC IP address is not configured according to the local networking requirements, network issues might occur.  
## Accessing OSC Console  
Access OSC console when troubleshooting network issues. OSC console access in VMware and OpenStack environments are shown below:

### VMware Deployments  
For OSC installed on VMware, access the ESXi host by using the native vSphere desktop application or the vSphere Web Client. 
Right-click on the virtual machine and select **Open Console**.

#### OSC IP Address and Netmask  
Check IP configuration on the OSC management port:   [`show network ip`](/references/cli.md/#user-content-show-network-ip)  
If the expected IP address and netmask are not shown, configure the IP address:  [`set network ip`](/references/cli.md/#user-content-set-network-ip)  

#### Gateway Address  
Check network route to view the default gateway IP address:  [`show network route`](/references/cli.md/#show-network-route)  
If the expected gateway address is not shown, configure the gateway address: [`set network gateway`](/references/cli.md/#user-content-set-network-gateway)  

#### Test Connection 
After verifying network configurations are correct, ping a remote IP address to test network connectivity by using the command:  
```
$ ping REMOTE_IP_ADDRESS
```
