#Troubleshooting OSC Web Access  
This procedure troubleshoots issues when OSC URL is not accessible using a web browser. OSC URL access problem might be due to couple of reasons. Follow the steps below to debug.

1. Ping OSC IP Address.  
   **Command**: `C>ping OSC_IP_Address`
2. If the ping  is **successful**:  
 2a. Access OSC CLI through an SSH client [OSC ssh shell](/gettingstarted/accessing.md#user-content-accessing-osc-through-cli). 

 2b. Check the server status by using the command [server status](/references/cli.md/#user-content-server-status).  

 2c. If OSC status is `Open Security Controller Server is running` you should be able to access the OSC URL.  
Otherwise there may be an environment problem on your client (i.e.: firewall configuration, browser settings, etc).  

 2d. If OSC status is `Open Security Controller Server is not running`. Start the server using the command  [server start](/references/cli.md/#user-content-server-start).  

3.  If ping has **failed**:  
 3a. Follow [network troubleshooting steps](/troubleshooting/network-troubleshooting/osc-networking.md).  
