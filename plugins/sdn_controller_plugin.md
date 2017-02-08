# SDN Controllers

The OSC SDN Plugin SDK contains the set of APIs that define the functionality OSC expects from an SDN controller. 
OSC communicates with the SDN controller through these interfaces to implement traffic redirection and notify the SDN layer of network changes.  


## Plugins SDK
Below is a high level description of the functionalities expected to be implemented by the plugin. For more details refer to the SDN Plugin interfaces documentation. 

### Status Check
The plugin must provide information regarding the availability of the SDN controller.

### Connectivity Configuration
The plugin must implement interfaces to allow for providing connectivity information like the SDN controller IP address, credentials, region, etc.

### Network Elements
Network elements are entities used by OSC to define [inspection hooks](#inspection-hooks). They contain information like IP addresses and MAC addresses and can correspond to various network entities like ports or port groups.  
These set of APIs comprises CRUD operations for network elements.

### Inspection Hooks
Inspection hooks are used by OSC to create, update or delete traffic redirections between the protected workloads and the security appliances.  Along with the [network elements](#network-elements) OSC provides failure policy information and VLAN tags when operating on inspection hooks.  
These set of APIs comprises CRUD operations for inspection books.



