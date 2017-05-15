# Troubleshooting OSC Web Access  

This procedure troubleshoots issues when the OSC web application is not accessible using a browser. Issues with accessing the OSC web application might be due to different reasons. Follow the steps below to debug:

1. Ping OSC IP Address:  
   ```
   $ ping OSC_IP ADDDRESS 
   
   ```
2. If pinging OSC succeeds:  
   * Access the [OSC CLI](/gettingstarted/accessing.md#user-content-accessing-osc-through-cli) through an SSH client. 

   * Check the server status by using the command [`server status`](/references/cli.md/#user-content-server-status).  

   * If the returned status is **Open Security Controller Server is running**, OSC should be accessible through its URL.  
If you are still unable to access the OSC web application, there may be an environment problem on your client (e.g.: firewall configuration, browser settings, etc).  

   * If the returned status is **Open Security Controller Server is not running**, start the server using the command [`server start`](/references/cli.md/#user-content-server-start).  

3.  If pinging OSC fails: 
    * Follow [network troubleshooting steps](/troubleshooting/osc-networking.md).  
