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
A virtual site provides a mechanism to perform operations on a group of sites, reducing the need to repeat the same set of operations for each site. Each label consists of a key-value pair. There are no limitations on the type of sites that can be grouped together. Both cloud and physical sites can be grouped together to create a virtual site. There are some prebuilt virtual sites in XC Console that define our Regional Edges as a grouping but chances are you are going to want to limit which Regions participate in vk8s. 

Ultimately, the virtual site defines where our virtual k8s pods will be running. In this lab we will be defining North American Regional Edges but others could easily be added just by modifying the label in the Virtual Site definition. 


Here is a look at the default Virtual Sites that could be used but we will be taking a more granular approach. 
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
Configure the RE workload flavor as shown in the screenshot. You have more options when using the Customer Edge exclusively for your vk8s implementation (covered further down in this article) 

<img width="781" alt="image" src="https://github.com/user-attachments/assets/7a23b89a-8c50-42cb-b274-bd5f8173cdbb">

Save and Exit and you should see this: 
<img width="1124" alt="image" src="https://github.com/user-attachments/assets/6a8120ba-059d-4e34-85be-a9ac9726952c">

Clicking on the vk8s name hyperlink will take you into the configurable area. Notice the 5 Regional Edge sites shown at the bottom based on our vsite USA definitions. 
<img width="1112" alt="image" src="https://github.com/user-attachments/assets/e138fdfa-709a-495a-a903-cab7e5f9255a">


### Download your Kubeconfig file (optional)
You can use the kubeconfig file locally to manage resources in XC Console vK8s and/or you can manage resources directly within the XC Console by pasting in your yaml definitions. We will look at both methods. 

Distributed Apps -> Applications -> Virtual K8s -> Click the "3 dots" under the Actions menu on the far right -> Kubeconfig -> (select a security conscious expiration date and treat this file appropriately as it contains highly sensitive data.  

<img width="1107" alt="image" src="https://github.com/user-attachments/assets/fee82676-cb7d-4037-802d-39d95271f503">

You will use this file with your local kubectl utility to authenticate and interact with the vk8s namespace. Feel free to open and inspect the file content...but keep it secure. 

### Workloads or Deployments?

**Deployments** are a native Kubernetes. You could simply paste in your deployment definition YAML or leverage kubectl to imperatively create the deployment. We will look at both options below. 

**Workloads** are not a native Kubernetes construct and are an abstract definition of groups of objects to be deployed like Deployments, StatefulSets, DaemonSets, Jobs, etc. Workloads are similar to deployments but one logical layer above them.....or one umbrella, making it easier for users to interact with Kubernetes resources in a more simplified, higher-level manner.

Use Deployments when managing stateless applications that need to be easily updated, scaled, and self-healed, with the flexibility to roll back updates in case of failure.

Use Workloads (StatefulSet, DaemonSet, Jobs) when you need specific functionality beyond just stateless applications. These are for cases requiring persistence, specialized task execution, or per-node pod scheduling.

<img width="1124" alt="image" src="https://github.com/user-attachments/assets/ac1ea09b-1811-487f-a8ec-af229ff5ba66">
<br>
<br>

> **Note:** The test container/image used in this setup is a public nginx container that runs unpriviledged. This is necessary per the restrictions listed above in the diagram. The container will natively start on a high port (8080) that does not require root to bind to. 

#### Workloads
Starting with the most commonly used method to deploy vk8s services, we will use a workload to deploy our example app. 
The workload will define our entire application, including the deployment, pods, service, load balancer and origin pool. 

Distributed Apps -> Applications -> Virtual K8s -> "Click on your vk8s name". 

From the Workloads tab, click "Add vK8s workload" and use the screenshot to configure the initial form.

<img width="774" alt="image" src="https://github.com/user-attachments/assets/8484d74d-1386-4c12-a2f2-6c6d64bc5776">

Click the blue "Configure" under "Service". 

Containers -> Add Item
Image Name: ```ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl```
<img width="745" alt="image" src="https://github.com/user-attachments/assets/fb24118b-09e4-4d38-8d6f-3cdfab89f835">

Deploy Options: 
Choose: "Regional Edge Virtual Site"
Click the blue "Configure" under "Regional Edge Virtual Sites"
<img width="845" alt="image" src="https://github.com/user-attachments/assets/b141c359-068f-4b37-937a-977b8e845803">

Use the USA based regional edge virtual site: 
<img width="336" alt="image" src="https://github.com/user-attachments/assets/29061427-6e81-4401-9502-6c2ae33ca6c0">

Advertise Options: 
Advertise on Internet vs Advertise in Cluster

Advertise on Internet does exactly what it sounds like. AoI preconfigures a service to expose the pods in vK8s, an origin pool referencing the service and finally a public globally-redundant load balancer. 
<img width="608" alt="image" src="https://github.com/user-attachments/assets/13eec596-e468-4ac4-83b3-15cad08974ca">

Advertise in Cluster 
This setting is similar to AoI and while this automatically creates a vK8s service, it does not create an origin pool or load balancer. It will be up to the admin to define the origin pool, create the load balancer and deploy it where necessary to provide access to the service.  


#### Deployments

The test container/image used in this setup is a public nginx container that runs unpriviledged. This is necessary per the restrictions listed above in the diagram. The container will natively start on a high port (8080) that does not require root to bind to. 

**Imperative**
```
kubectl --kubeconfig=vk8s.kubeconfig create deployment nginx-imper --image=ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl
```
**Nodeport** (Need to test)
```
kubectl --kubeconfig=vk8s.kubeconfig expose deployment nginx-imper --type=NodePort --port=2000 --target-port=8080 --node-port=32000
```
**ClusterIP** (Need to test)
```
kubectl --kubeconfig=vk8s.kubeconfig expose deployment nginx-imper --type=ClusterIP --port=2000 --target-port=8080
```

**Declarative**
```
kind: Deployment
metadata:
  name: nginx-declar
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl
          ports:
            - containerPort: 1024
          resources:
            limits:
              cpu: "1"
              memory: "200Mi"
            requests:
              cpu: "0.5"
              memory: "100Mi"
```






## CE vk8s

## RE + CE vk8s


