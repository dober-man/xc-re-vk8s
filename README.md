# xc-vk8s
Start here for a thorough overview of vK8s and deployment considerations: 
https://docs.cloud.f5.com/docs-v2/platform/concepts/distributed-apps-management#virtual-kubernetes-vk8s

You can deploy vK8s in 3 architectures. Each has unique advantages and unique restrictions. 

* RE Only
* RE + CE
* CE Only

## RE Only vk8s
This simplified diagram shows how globally distributed users access resources closest to their region. In reality there are dozens of XC PoPs all across the planet and due to how anycast works, users will automatically route to the closest destination to their source. The vK8s namespace is replicated across all the PoPs making the pods globally redundant and available closest to the source. 

<img width="994" alt="image" src="https://github.com/user-attachments/assets/d8f532c4-0bb2-415a-9b51-4207f501fece">


### The Setup
"RE Only" vK8's is by far the easiest way to get started although it comes with a decent list of restictions as shown in the diagram. The upside is, it is extremely easy to get configured, 100% on our infrastucture (nothing to manage) and it's globally distributed with a ton of front door security features. 

#### Virtual Site
A virtual site provides a mechanism to perform operations on a group of sites, reducing the need to repeat the same set of operations for each site. Each label consists of a key-value pair. There are no limitations on the type of sites that can be grouped together. Both cloud and physical sites can be grouped together to create a virtual site. There are some prebuilt virtual sites in XC Console that define our Regional Edges as a grouping but chances are you are going to want to limit which Regions participate in vk8s. 

<img width="922" alt="image" src="https://github.com/user-attachments/assets/7bab32c8-8359-44d7-b401-6c473038937d">

Define a Custom Virtual Site: 








## CE vk8s

## RE + CE vk8s


