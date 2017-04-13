## Maintaining Appliance Instances

When you create a distributed appliance, you enable the required virtualization connectors in the distributed appliance. For every enabled virtualization connector, OSC creates a virtual security system (virtual system) by default. In case of IPS, this virtual security system in the distributed appliance corresponds to the virtual security system in the Network Security Manager.

A virtual security system is assigned a name by assigning a sequentially increasing number to the name of the distributed appliance. When you deploy this virtual security system on the required cluster, NSX automatically installs a virtual security appliance in each ESXi host of the cluster. In the case of IPS and firewall services, a virtual security appliance corresponds to the virtual security system instance, that is the virtual IPS Sensor installed in each ESXi host.

After you deploy the virtual security system, you can view and maintain deployed virtual appliances from the **Appliance Instances** page of OSC.

### Task
TBD
