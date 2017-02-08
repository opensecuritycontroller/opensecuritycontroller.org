# OSC Plugins

The OSC plugin model allows integration with VNF security managers and the SDN layer of the virtualized environment.  OSC defines two distinct plugins: **Security Manager Plugin** and **SDN Controller Plugin**.  Both of these plugins are defined through a set of java interfaces that describes the functionality OSC expects from the plugins.  
Through this mechanism OSC can invoke security managers or SDN controllers APIs without knowing their details. The implemented plugins bridges the gap between specific APIs exposed by these external services and the interfaces/contract expected by OSC.  


## Plugins SDK
The first step towards developing OSC plugins is downloading the OSC dependencies containing the java interfaces that define the plugins functionality. We refer to these dependencies as the OSC Plugins SDK.  
You can download the SDKs directly from the OSC web UI:

1. Login to OSC web UI.
2. Navigate to Manage -> Plugins
3. Download the plugin SDK:
	* For the **SDN Controller Plugins** SDK:  
		Select the tab "SDN Controller Plugins".  
		Click "Download SDN Controller Plugin SDK".
	* For the **Security Manager Plugin** SDK:  
	    Select the tab "Manager Plugins".  
		Click "Download SDN Controller Plugin SDK".

![DownloadSDk](./images/sdk_download.png)  
*OSC Plugins View*


## Building And Testing OSC Plugins
The OSC server uses OSGi technology to provide a dynamic plugin model. For details on how to build and test OSC plugins see **[Building OSC Plugins with OSGi](./osgi_plugin.md)**.

## Plugins

* For more details on the OSC manager plugins see **[Security Manager Plugins](./security_mgr_plugin.md)**.
* For more details on the OSC SDN controller plugins see **[SDN Controller Plugins](./sdn_controller_plugin.md)**.