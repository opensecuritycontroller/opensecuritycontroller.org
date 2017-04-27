
## OSC Requirements  

The success of OSC relies on communication to and from other services including an SDN controller, a virtualization provider such as OpenStack, and a security manager. 

To use OSC, the following requirements must be met: 

## SDN Controller  
To implement traffic redirection and SDN notifications, OSC requires two components which can be obtained from an OSC compatible SDN controller vendor: 
* an SDN controller component installed in the virtualized environment.
* an SDN controller plugin to be installed in OSC.

## Security Manager  
To enforce policies by applying security functions in a virtualized environment, OSC requires these components obtained from an OSC compatible security manager vendor such as a: 
* security manager instance with a reachable IP address.
* security appliance image to be uploaded in OSC.
* security manager plugin to be installed in OSC.

## OpenStack   
A compatible virtualization provider is needed to protect virtual assets. The requirements for using OpenStack are as follows:
* OpenStack **Mitaka** composed of controller, compute, and network nodes.
* OpenStack services such as Keystone, Nova, Neutron, and Glance must be reachable by OSC. 
* The following minimum network topology:
  * One **Management Network** primarily for communication between the security manager and the distributed appliance instance deployed by OSC.  
  
     > Note: If the security manager is externally hosted, both a router and an **External Network** are needed for the security manager to communicate to the distributed appliance instance. The network should be configured as *shared* and *external*. 
  * One **Inspection Network** for which redirected traffic will be intercepted. 
* Both a tenant and domain (region).
* Installed and configured SDN controller.

## OSC Virtual Appliance
To install and access the OSC virtual appliance, the following requirements must be met:

* Corresponding firewalls are configured to allow HTTPS over ports `443` and `8090`.
 * The web application communicates over port `443`.
 * OSC communicates with other servers over port `8090`.
* Recommended browsers to access the OSC web application:
  * Internet Explorer version 11 or newer.
  * Google Chrome version 39 or newer.
  * Mozilla Firefox version 33 or newer.
  * Safari 8.0 or newer.
* Accessibility to the OSC installation file (.ovf).
* An available IP address, subnet mask, gateway, and DNS server for the OSC virtual appliance.
 * When the OSC virtual appliance is configured with IP settings, it should be [reachable](/gettingstarted/accessing.md) from the client machine.

