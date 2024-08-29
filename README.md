# xc-vk8s
Start here for a thorough overview of vK8s and deployment considerations: 
https://docs.cloud.f5.com/docs-v2/platform/concepts/distributed-apps-management#virtual-kubernetes-vk8s

## RE vk8s
In this simplified diaggram this shows how globally distributed users access resources closest to their region. In reality there are dozens of XC PoPs all across the planet and due to how anycast works, users will automatically route to the closest destination to their source. The vK8s namespace is relipcated across all the PoPs making the pods globally redundant and available closest to the source. 

<img width="1017" alt="image" src="https://github.com/user-attachments/assets/09fac75c-5297-4df9-a01d-db4598677337">

### The Setup
RE Only vK8's is by far the easiest way to get started although it comes with a decent list of restictions as shown in the diagram. The upside is, it is extremely easy to get configured and it's globally distributed with a ton of front door security features. 




## CE vk8s

## RE + CE vk8s


