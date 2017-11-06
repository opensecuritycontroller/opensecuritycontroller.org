# Tutorial: Protecting Containerized Assets with OSC on Kubernetes

Given an existing [Kubernetes compatible environment](/gettingstarted/requirements.md#user-content-kubernetes) and a [deployed instance of OSC](/gettingstarted/installing_ost.md), this tutorial provides the necessary steps for setting up OSC to protect a single workload instance in Kubernetes. 

These steps include: 
* Deployment of a security appliance instance.
* Definition of the workload under protection.
* Redirection of the traffic to be protected through the security appliance.

## Setup Requirements

### Virtualization Environment  

In Kubernetes, create two pods:
1. The **attacker** pod:  
This pod will have the container from which we will *ping* the victim pod. 
Create the pod configuration file:
```sh
$ cat >~/attacker-busybox.yaml <<EOL
apiVersion: v1
kind: Pod
metadata:
  name: attacker-busybox
  namespace: default
spec:
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: attacker-busybox
  restartPolicy: Always
EOL
```
Create the pod:
```sh
$ kubectl create -f ~/attacker-busybox.yaml
```
2. The **protected** pod:  
The protected pod that will be pinged by the attacker. Observe it has a label which will be used later for creating a security group member in OSC.  
Create the pod configuration file:  
```sh
$ cat >~/victim-busybox.yaml <<EOL
apiVersion: v1
kind: Pod
metadata:
  name: victim-busybox
  namespace: default
  labels:
    env: production
spec:
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: victim-busybox
  restartPolicy: Always
EOL
```
Create the pod:  
```sh
$ kubectl create -f ~/victim-busybox.yaml
```

### Security Appliance and Manager  

There are two options for obtaining a security appliance image and its corresponding manager plugin. The first option is to use an appliance container image and manager plugin provided by a security manager vendor compatible with OSC. The second option is to manually create them. 

For this tutorial, it is assumed that the appliance image and security plugin will be manually created:  

* The [`SAMPLE MANAGER PLUGIN`](https://github.com/opensecuritycontroller/security-mgr-sample-plugin) is a dummy plugin that is available along with OSC. 

### SDN Controller  

OSC requires two components to implement traffic redirection and SDN notifications through an SDN controller, an SDN component and an SDN controller plugin. You may acquire these from an OSC compatible vendor or you can manually create them.

For this tutorial, it is assumed that the SDN component and SDN controller plugin will be manually created:   
* The [`SDN CONTROLLER NSC PLUGIN`](/plugins/plugins.md) is uploaded on OSC, enabling communication between the SDN controller and OSC.  
> Note: This plugin is configured to NOT support port grouping. In order for it to work with this tutorial it must be configured with [`SUPPORT_PORT_GROUP` as true](https://github.com/opensecuritycontroller/sdn-controller-nsc-plugin/blob/master/src/main/java/org/osc/controller/nsc/api/SampleSdnControllerApi.java#L54) and recompiled. Optionally you can also use the [SDN Nuage Plugin](https://github.com/opensecuritycontroller/osc-nuage-plugin) if you have a Kubernetes environment configured with Nuage SDN controller.  

## Set up OSC to Protect a Workload

### 1. Upload Plugin

Within OSC, navigate to **Manage** > **Plugins** using the left-hand menu. 
* Upload the `SDN CONTROLLER NSC PLUGIN`.  
* Upload the `SAMPLE MANAGER PLUGIN`.  

![Upload Plugins](images/add_plugins.jpg)  
*Upload SDN Controller and Manager Plugins*

### 2. Define Virtualization Connector  

Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Virtualization Connectors`:
* `POST /api/server/v1/virtualizationConnectors` with json payload:
```json
{
  "name": "k8s-vc",
  "type": "KUBERNETES",
  "controllerIP": "string",  # IP Address of the SDN controller. Not needed if using the NSC SDN Plugin.
  "controllerUser": "string", # User name of the SDN controller. Not needed if using the NSC SDN Plugin.
  "controllerPassword": "string", # Password of the user of the SDN controller. Not needed if using the NSC SDN Plugin.
  "providerIP": "string", # IP Address for the kube API service.
  "providerUser": "string", # User name for accessing the kube API service.
  "providerPassword": "string", # Password for the user name accessing the kube API service.
  "softwareVersion": "v1.7",
  "controllerType": "NSC" # Or 'Nuage' if using Nuage SDN
}
```

You should get a response body containing an ID, similar to this `"id":VIRTUALIZATION_CONNECTOR_ID`, copy this identifier to be used on a later steps.

### 3. Define Manager Connector  

Using the left-hand menu, navigate to **Setup** > **Manager Connectors**, and then select **Add**.
* Enter a name.
* For the type, select **ISM** as described by the `SAMPLE APPLIANCE IMAGE` and the `SAMPLE MANAGER PLUGIN`.
* Enter the IP address `1.1.1.1`, and then the credentials of `abc / 123`.
 	>Note: When using a real security manager, use the real IP address and credentials.

![Add Manager Connector](images/add_mc.jpg)  
*Add Manager Connector*  

After adding the manager connector, ensure the **Last Job Status** is **PASSED** and that policies defined in the security manager are populated under **Polices** on the bottom-half of the page.

### 4. Define Service Function  

#### 4.1. Create the Appliance Model  
Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Security Functions Catalog`:  
* `POST /api/server/v1/catalog` with json payload:
```json
{
"model":"test-model",
"managerVersion":"1.0",
"managerType": "ISM"
}
```

You should get a response body containing an ID, similar to this `"id":APPLIANCE_MODEL_ID`, copy this identifier to be used on later steps.


#### 4.2. Create the Appliance Version
Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Security Functions Catalog`:  
* `POST /api/server/v1/catalog/{applianceId}/versions` with json payload:  
**applianceId:** `APPLIANCE_MODEL_ID`
```json
{
"parentId":"APPLIANCE_MODEL_ID",
"swVersion":"0.1",
"virtualizationType":"KUBERNETES",
"virtualizationVersion":"v1.7",
"imageUrl":"corfr/tcpdump"
}
```

You should get a response body containing an ID, similar to this `"id":APPLIANCE_SOFTWARE_VERSION_ID`, copy this identifier to be used on later steps.

### 5. Define Distributed Appliance  

#### 5.1. Create the Distributed Appliance  

Using the left-hand menu, navigate to **Setup** > **Distributed Appliance**. Under **Distributed Appliances**, select **Add**.
* Enter a name.
* Choose the previously created **manager connector** for the manager connector.
* Choose the appliance model that was previously imported into the security function catalog.
* Select the **Enabled** box.

After creating the distributed appliance, ensure that the **Last Job Status** is **PASSED**.

#### 5.2. Retrieve the Virtual System ID  
The creation of the distributed appliance will also generate a virtual system.  In order to perform later you will the virtual system ID. To retrieve that perform the following steps:

Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Distributed Appliances`: 
* `GET /api/server/v1/distributedAppliances`  

You should get a response body containing the ID of the virtual system, similar to this `"virtualSystem": [ { "id":VIRTUAL_SYSTEM_ID ... } ]`, copy this identifier to be used on later steps.  


### 6. Define Deployment Specification  

### 6.1. Register the Deployment Pod Port  
Because we are using the NSC SDN Controller stub plugin we must register the port element for the deployed VNF pod. This can be done using the test API:  
* `POST https://10.3.205.173:8090/nsc/controller/10.3.205.177/networkElements/1` with json payload:  
```json
{
"elementId":1,
"parentId":"1",
"deviceOwnerId":"default:testds",
"macAddresses":  ["00:00:00:00:00:01"],
"portIPs": ["70.70.0.1"]
}
```

> Note:  This step is not needed if you are using a real SDN controller plugin (as opposed to a stub) like the *OSC Nuage plugin*. The SDN controller should already have and be able to provide the pod port to OSC after a pod is created on Kubernetes.  

### 6.2 Create the Deployment Specification  

Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Virtual Systems`: 
* `POST /api/server/v1/virtualSystems/{vsId}/deploymentSpecs` with json payload:  
**vsId**: `VIRTUAL_SYSTEM_ID`  
```json
{
 "name": "testds", 
 "namespace": "default", 
 "count": 1
}
```

On the OSC UI you should see a deployment spec `testds` with the **Last job Status** **PASSED**.  
**Troubleshooting:**   
If the job failed perform the following steps to troubleshoot:
1. Ensure that you are able to create pods on the Kubernetes cluster using the image `corfr/tcpdump`.  
2. Ensure that OSC can communicate with the Kubernetes API server through the [proxy](/gettingstarted/requirements.md#user-content-kubernetes).  


### 7. Define Security Group  

#### 7.1. Register the Protected Pod Port  
Because we are using the NSC SDN Controller stub plugin we must register the port element for the protected pod. This can be done using the test API:  
* `POST https://10.3.205.173:8090/nsc/controller/10.3.205.177/networkElements/2` with json payload:  
```json
{
"elementId":2,
"parentId":"1",
"deviceOwnerId":"default:victim-busybox",
"macAddresses":  ["00:00:00:00:00:02"],
"portIPs": ["70.70.0.2"]
}
```

> Note:  This step is not needed if you are using a real SDN controller plugin (as opposed to a stub) like the *OSC Nuage plugin*. The SDN controller should already have and be able to provide the pod port to OSC after a pod is created on Kubernetes.  

#### 7.2. Create the Security Group
Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Virtualization Connectors`:  
* `POST /api/server/v1/virtualizationConnectors/{vcId}/securityGroups` with json payload:  
**vcId**: `VIRTUALIZATION_CONNECTOR_ID`  
```json
{
"name": "testSG"
}
```
You should get a response body containing the ID of the create security group, similar to  `"id": SECURITY_GROUP_ID`, copy this identifier to be used on later steps.

#### 7.3. Add a Label Member  
Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Virtualization Connectors`:  
* `PUT /api/server/v1/virtualizationConnectors/{vcId}/securityGroups/{sgId}/members` with json payload:  
**vcId**: "VIRTUALIZATION_CONNECTOR_ID"
**sgId**: "SECURITY_GROUP_ID"
```json
{
	"parentId": VIRTUALIZATION_CONNECTOR_ID,
	"id": SECURITY_GROUP_ID,
	"members": [
	 {
		"name": "env-production",
		"type": "LABEL",
		"label": "env=production"
	  }
	]
}
```

On the OSC UI you should see the **Last Job Status**  of the created security group is **PASSED**. When clicking on **Membership** you should see one member in the security group with the IP address `70.70.0.2`.  


### 8. Bind Security Group  
Using the OSC api-doc `https://OSC_IP:8090/api-doc -> Operations for Virtualization Connectors`:  
* `PUT /api/server/v1/virtualizationConnectors/{vcId}/securityGroups/{sgId}/bindings` with json payload:  
**vcId**: `VIRTUALIZATION_CONNECTOR_ID`  
**sgId**: `SECURITY_GROUP_ID`  
```json
	[
	  {
	    "virtualSystemId": "VIRTUAL_SYSTEM_ID",
	    "name": "sgbind",
	    "policyIds": [
	      "POLICY_ID" # Retrieve from GET /api/server/v1/virtualSystems/{vsId}/policies
	    ],
	    "order": 0,
	    "markedForDeletion": false,
	    "binded": true
	  }
	]
```

After binding, ensure that the **Last Job Status** is **PASSED**.

## Validate the Setup 

#### VNF Deployment  

After setting up OSC and deploying a **Distributed Appliance Instance** through the creation of a **Deployment Spec**, verify the **Distributed Appliance Instance** was deployed on Kubernetes: 
```sh
$  kubectl get deployments
NAME        DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
testds-XX   1         1         1            1           3h
```
There should be one deployment prefixed with the name of the deployment spec created on OSC.  

You can also see the corresponding pod:
```sh
$ kubectl get pods | grep testds
testds-XX-XXXXXXXXX   1/1       Running
```

## Appendix

### Network Redirection

When using a real SDN controller plugin, like [Nuage](https://github.com/opensecuritycontroller/osc-nuage-plugin), you should be able to validate the traffic redirection when a policy is bound to a security group. For that you can perform the following steps on your Kubernetes cluster:  

1. SSH to the node hosting `attacker-busybox` pod. To know which Kubernetes node is hosting the pod you can run:  
```sh
$ kubectl describe pods attacker-busybox | grep Node
Node:           minion01
```

2. SSH to the node hosting the security VNF pod. To know which Kubernetes node is hosting the security VNF pod you can run:  
```sh
$ kubectl describe pods testds-XX-XXXXXXXXX  | grep Node  # Replace with the real pod name
Node:           minion01
```

3. From the SSH session for the `attacker-busybox` identify the container ID (`ATTACKER_CONTAINER_ID`) and IP address (`ATTACKER_IP_ADD`) of  the `attacker-busybox`:  
```sh
$ docker ps | grep k8s_attacker-busybox
```
```sh
$ docker exec ATTACKER_CONTAINER_ID ping ip add
```

4.  From the SSH session for the `attacker-busybox` ping the protected pod (`victim-busybox`) from within the attacker container:  
```sh
$ docker exec ATTACKER_CONTAINER_ID ping PROTECTED_POD_IP # You can get the PROTECTED_POD_IP from the OSC UI by looking at the members (Membership) of the created security group.
```
If the security group is bound the ping operation should not reach the target.  

5. From the SSH session for the security VNF pod identify the ID (`VNF_CONTAINER_ID`) of the VNF container:  
```sh
$ docker ps | grep k8s_testds
```

6. From the SSH session for the security VNF pod inspect the traffic from within the VNF container:  
```sh
$ docker exec VNF_CONTAINER_ID tcpdump -i eth0 | grep ATTACKER_IP_ADD
```

If the security group is bound the tcpdump command should show flowing traffic.

