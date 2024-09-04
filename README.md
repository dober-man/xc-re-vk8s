# xc-vk8s
Virtual Kubernetes (vK8s) in F5 Distributed Cloud allows users to run Kubernetes workloads across multiple cloud environments without managing the underlying Kubernetes control plane. It offers seamless integration with F5’s Distributed Cloud Services, enabling centralized management, scaling, and security for Kubernetes applications across hybrid and multi-cloud infrastructures.

Start here for a thorough overview of vK8s and deployment considerations: 
https://docs.cloud.f5.com/docs-v2/platform/concepts/distributed-apps-management#virtual-kubernetes-vk8s

You can deploy vK8s in 3 architectures. Each has unique advantages and unique restrictions. 

* RE Only
* RE + CE
* CE Only

## RE Only vk8s
This simplified diagram shows how globally distributed users access resources closest to their region. In reality there are dozens of XC PoPs all across the planet and due to how anycast works, users will automatically route to the closest destination to their source. The vK8s namespace is replicated across all the PoPs making the pods globally redundant and available closest to the source. 

<img width="985" alt="image" src="https://github.com/user-attachments/assets/d90f7a00-c40a-468c-95b2-fc97ee5116dc">


### The Setup
"RE Only" vK8's is by far the easiest way to get started although it comes with a decent list of restictions as shown in the diagram. The upside is, it is extremely easy to get configured, 100% on our infrastucture (nothing to manage) and it's globally distributed with a ton of front door security features. 

#### Virtual Site
A virtual site provides a mechanism to perform operations on a group of sites, reducing the need to repeat the same set of operations for each site. Each label consists of a key-value pair. There are no limitations on the type of sites that can be grouped together. Both cloud and physical sites can be grouped together to create a virtual site. There are some prebuilt virtual sites in XC Console that define our Regional Edges as a grouping but chances are you are going to want to limit which Regions participate in vk8s. Ultimately, the virtual site defines where our virtual k8s pods will be running. In this lab we will be defining North American Regional Edges but others could easily be added just by modifying the label in the Virtual Site definition. 

<img width="922" alt="image" src="https://github.com/user-attachments/assets/7bab32c8-8359-44d7-b401-6c473038937d">

Define a Custom Virtual Site for just USA based Regional Edges: 

Distributed Apps -> Manage -> Virtual Sites -> "Add Virtual Site"
* Name: my-vsite-usa
* Site Type: RE
* Site Selector Expression: "Add Label" -> ves.io/country -> in -> ves.io.usa <br>
Save & Exit

<img width="721" alt="image" src="https://github.com/user-attachments/assets/d1c7313a-6369-4768-8ff3-f9eef9d430a2">


#### Virtual K8s
Distributed Apps -> Applications -> Virtual K8s -> "Add Virtual K8s" (You can have one instance of vk8s per XC tenant namespace)
* Name: vk8s-1
* Virtual Sites: default/my-vsite-usa
* Default Workload Flavor: "Add Item"
  
These values are not arbitrary and must be defined appropriately given this deployement model uses our Regional Edges which have finite resources. 
Configure the RE workload flavor as show in the screenshot. 

<img width="781" alt="image" src="https://github.com/user-attachments/assets/7a23b89a-8c50-42cb-b274-bd5f8173cdbb">

<img width="772" alt="image" src="https://github.com/user-attachments/assets/6d190368-3b90-4da2-90a7-79badf46e1b3">















## CE vk8s

## RE + CE vk8s


