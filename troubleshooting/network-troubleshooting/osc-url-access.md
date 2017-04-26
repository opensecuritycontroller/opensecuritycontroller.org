#Troubleshooting OSC Web Access  
This procedure troubleshoots issues when OSC URL is not accessible using a web browser. Issues with OSC URL access might be due to different reasons. Follow the steps below to debug:

1. Ping OSC IP Address:  
   **Command**: `$ ping OSC_IP ADDDRESS`
2. If pinging OSC succeeds:  
   * Access the [OSC CLI](../../gettingstarted/accessing.md#accessing-osc-through-cli) through a SSH client [OSC ssh shell](../../gettingstarted/accessing.md#accessing-osc-through-cli). 

   * Check the server status by using the command [`server status`](../../references/cli.md/#server-status).  

   * If the returned status is **Open Security Controller Server is running**, OSC should be accessible through its URL.  
If you are still unable to access OSC URL, there may be an environment problem on your client (i.e.: firewall configuration, browser settings, etc).  

   * If the returned status is **Open Security Controller Server is not running**, start the server using the command [`server start`](../../references/cli.md/#server-start).  

3.  If pinging OSC fails: 
    * Follow [network troubleshooting steps](./osc-networking.md).  
