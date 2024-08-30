# xc-vk8s
Start here for a thorough overview of vK8s and deployment considerations: 
https://docs.cloud.f5.com/docs-v2/platform/concepts/distributed-apps-management#virtual-kubernetes-vk8s

You can deploy vK8s in 3 architectures. Each has unique advantages and unique restrictions. 

* RE Only
* RE + CE
* CE Only

## RE Only vk8s
This simplified diagram shows how globally distributed users access resources closest to their region. In reality there are dozens of XC PoPs all across the planet and due to how anycast works, users will automatically route to the closest destination to their source. The vK8s namespace is replicated across all the PoPs making the pods globally redundant and available closest to the source. 

<img width="838" alt="image" src="https://github.com/user-attachments/assets/855d3f78-f84d-4070-a404-5e4fbf2e9728">


### The Setup
"RE Only" vK8's is by far the easiest way to get started although it comes with a decent list of restictions as shown in the diagram. The upside is, it is extremely easy to get configured, 100% on our infrastucture (nothing to manage) and it's globally distributed with a ton of front door security features. 



## CE vk8s

## RE + CE vk8s


