# SDN Controllers

The Open Source Controller (OSC) SDN Plugin SDK contains the set of APIs that define the functionality OSC expects from an SDN controller. OSC communicates with the SDN controller through these interfaces to implement traffic redirection and notify the SDN layer of network changes.  

## SDN Controller Plugin SDK
Below is a high level description of the functionalities expected to be implemented by the plugin. For more details, refer to the  `javadoc`of the interfaces defined by this SDK. 

### Status Check
The plugin must provide information regarding the availability of the SDN controller.

### Connectivity Configuration
The plugin must implement interfaces to allow for providing connectivity information like the SDN controller IP address, credentials, region, etc.

### Network Elements
Network elements are entities used by OSC to define [inspection hooks](#user-content-inspection-hooks). They contain information like IP addresses and MAC addresses and can correspond to various network entities like ports or port groups. 

This set of APIs comprises CRUD operations for network elements.

### Inspection Hooks
Inspection hooks are used by OSC to create, update, or delete traffic redirections between the protected workloads and the security appliances.  Along with the [network elements](#network-elements), OSC provides failure policy information and VLAN tags when operating on inspection hooks.  

This set of APIs comprises CRUD operations for inspection books.

### Plugin Properties
In addition to the functionalities mentioned above, this SDK also specificies a set of required properties that must be provided when [registering the plugin implementation as an OSGi service](/plugins/osgi_plugin.md#user-content-exposing-the-service-provided-by-the-plugin). These properties will be used by OSC to identify and correctly use the plugin.  For more details and the full list of required properties, see the `javadoc` of the interface `org.osc.sdk.controller.api.SdnControllerApi` defined by this SDK.  
