## RE vK8s
This simplified diagram shows how globally distributed users access resources nearest to their region. In practice, there are dozens of F5 Distributed Cloud Points of Presence (PoPs) across the globe. Thanks to Anycast, users are automatically routed to the closest PoP based on their location, ensuring lower latency and improved performance. 

The virtual Kubernetes (vK8s) namespace can be replicated across all PoPs or deployed selectively to specific ones, offering redundancy and ensuring services are available close to the users. Furthermore, the XC vK8s service includes built-in replication and scaling mechanisms that adjust to application needs, ensuring efficiency and availability.

<img width="964" alt="image" src="https://github.com/user-attachments/assets/756b2dfd-071b-4d2d-a670-0cb95bdb331d">


### The Setup
"RE vK8's" is the simplest way to get started although it comes with some restrictions as shown in the diagram. The upside is, it is easy to get configured, 100% on our infrastucture (nothing to manage) and it's globally distributed with a ton of front-door security features. 

#### Virtual Site
A virtual site provides a mechanism to perform operations on a group of sites, reducing the need to repeat the same set of operations for each site. Both cloud and physical sites can be grouped together to create a virtual site. There are some prebuilt virtual sites in XC Console that define all of our Regional Edges as a grouping but, you may want to limit which Regions participate in vK8s. 

Ultimately, the "Virtual Site" defines where our virtual k8s pods will be running. In this lab we will be defining 5 USA Regional Edges but others could easily be added just by modifying the label in the Virtual Site definition. 


Here is a look at the default Virtual Sites that could be used but we will be taking a more granular approach. 
<img width="922" alt="image" src="https://github.com/user-attachments/assets/7bab32c8-8359-44d7-b401-6c473038937d">

#### Define a Custom Virtual Site for just USA based Regional Edges 

Distributed Apps -> Manage -> Virtual Sites -> "Add Virtual Site"
* Name: my-vsite-usa
* Site Type: RE
* Site Selector Expression: "Add Label" -> ves.io/country -> in -> ves.io.usa <br>
**Save & Exit**

<img width="721" alt="image" src="https://github.com/user-attachments/assets/d1c7313a-6369-4768-8ff3-f9eef9d430a2">


#### Virtual K8s
Distributed Apps -> Applications -> Virtual K8s -> "Add Virtual K8s" (You can have one instance of vK8s per XC tenant namespace)
* Name: vK8s-1
* Virtual Sites: default/my-vsite-usa
* Default Workload Flavor: "Add Item"
  
These values are not arbitrary and must be defined appropriately given this deployment model uses our Regional Edges which have finite resources. 
Configure the RE workload flavor as shown in the screenshot. You have more options when using the Customer Edge exclusively for your vK8s implementation. 

<img width="781" alt="image" src="https://github.com/user-attachments/assets/7a23b89a-8c50-42cb-b274-bd5f8173cdbb">

Save and Exit and you should see this: 
<img width="1124" alt="image" src="https://github.com/user-attachments/assets/6a8120ba-059d-4e34-85be-a9ac9726952c">

Clicking on the vK8s name hyperlink will take you into the configurable area. Notice the 5 Regional Edge sites shown at the bottom based on our vsite USA definitions. 
<img width="1112" alt="image" src="https://github.com/user-attachments/assets/e138fdfa-709a-495a-a903-cab7e5f9255a">


### Download your Kubeconfig file (optional)
You can use the kubeconfig file locally to manage resources in XC Console vK8s and/or you can manage resources directly within the XC Console by pasting in your yaml definitions. We will look at both methods. 

Distributed Apps -> Applications -> Virtual K8s -> Click the "3 dots" under the Actions menu on the far right -> Kubeconfig -> select a security conscious expiration date and **treat this file appropriately as it contains highly sensitive data.**  

<img width="1107" alt="image" src="https://github.com/user-attachments/assets/fee82676-cb7d-4037-802d-39d95271f503">

You will use this file with your local kubectl utility to authenticate and interact with the vK8s namespace. Feel free to open and inspect the file content...but **keep it secure.**

**This concludes the basic setups for re-vk8s**


