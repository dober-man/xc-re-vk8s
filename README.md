# XC vK8s
Virtual Kubernetes (vK8s) in F5 Distributed Cloud allows users to run Kubernetes workloads across multiple cloud environments without managing the underlying Kubernetes control plane. It offers seamless integration with F5’s Distributed Cloud Services, enabling centralized management, scaling, and security for Kubernetes applications across hybrid and multi-cloud infrastructures.

Start here for a thorough overview of vK8s and deployment considerations: 
https://docs.cloud.f5.com/docs-v2/platform/concepts/distributed-apps-management#virtual-kubernetes-vK8s

You can deploy vK8s in 3 architectures. Each has unique advantages and unique restrictions. 

* RE Only
* RE + CE
* CE Only

## RE Only vK8s
This simplified diagram shows how globally distributed users access resources closest to their region. In reality there are dozens of XC PoPs all across the planet and due to how anycast works, users will automatically route to the closest destination to their source. The vK8s namespace is replicated across all the PoPs making the pods globally redundant and available closest to the source. 

<img width="985" alt="image" src="https://github.com/user-attachments/assets/d90f7a00-c40a-468c-95b2-fc97ee5116dc">


### The Setup
"RE Only" vK8's is by far the easiest way to get started although it comes with a decent list of restictions as shown in the diagram. The upside is, it is extremely easy to get configured, 100% on our infrastucture (nothing to manage) and it's globally distributed with a ton of front door security features. 

#### Virtual Site
A virtual site provides a mechanism to perform operations on a group of sites, reducing the need to repeat the same set of operations for each site. Each label consists of a key-value pair. There are no limitations on the type of sites that can be grouped together. Both cloud and physical sites can be grouped together to create a virtual site. There are some prebuilt virtual sites in XC Console that define our Regional Edges as a grouping but chances are, you may want to limit which Regions participate in vK8s. 

Ultimately, the "Virtual Site" defines where our virtual k8s pods will be running. In this lab we will be defining USA Regional Edges but others could easily be added just by modifying the label in the Virtual Site definition. 


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
Distributed Apps -> Applications -> Virtual K8s -> "Add Virtual K8s" (You can have one instance of vK8s per XC tenant namespace)
* Name: vK8s-1
* Virtual Sites: default/my-vsite-usa
* Default Workload Flavor: "Add Item"
  
These values are not arbitrary and must be defined appropriately given this deployment model uses our Regional Edges which have finite resources. 
Configure the RE workload flavor as shown in the screenshot. You have more options when using the Customer Edge exclusively for your vK8s implementation (covered further down in this article) 

<img width="781" alt="image" src="https://github.com/user-attachments/assets/7a23b89a-8c50-42cb-b274-bd5f8173cdbb">

Save and Exit and you should see this: 
<img width="1124" alt="image" src="https://github.com/user-attachments/assets/6a8120ba-059d-4e34-85be-a9ac9726952c">

Clicking on the vK8s name hyperlink will take you into the configurable area. Notice the 5 Regional Edge sites shown at the bottom based on our vsite USA definitions. 
<img width="1112" alt="image" src="https://github.com/user-attachments/assets/e138fdfa-709a-495a-a903-cab7e5f9255a">


### Download your Kubeconfig file (optional)
You can use the kubeconfig file locally to manage resources in XC Console vK8s and/or you can manage resources directly within the XC Console by pasting in your yaml definitions. We will look at both methods. 

Distributed Apps -> Applications -> Virtual K8s -> Click the "3 dots" under the Actions menu on the far right -> Kubeconfig -> select a security conscious expiration date and **treat this file appropriately as it contains highly sensitive data.**  

<img width="1107" alt="image" src="https://github.com/user-attachments/assets/fee82676-cb7d-4037-802d-39d95271f503">

You will use this file with your local kubectl utility to authenticate and interact with the vK8s namespace. Feel free to open and inspect the file content...but keep it secure. 

### Workloads or Deployments?

**Deployments** are a native Kubernetes construct. You could simply paste in your deployment definition YAML or leverage kubectl to imperatively create the deployment. We will look at both options below. 

**Workloads** are not a native Kubernetes construct and are an abstract definition of groups of objects to be deployed like Deployments, StatefulSets, DaemonSets, Jobs, etc. Workloads are similar to deployments but one logical layer above them.....or one umbrella, making it easier for users to interact with Kubernetes resources in a more simplified, higher-level manner.

Use Deployments when managing stateless applications that need to be easily updated, scaled, and self-healed, with the flexibility to roll back updates in case of failure.

Use Workloads (StatefulSet, DaemonSet, Jobs) when you need specific functionality beyond just stateless applications. These are for cases requiring persistence, specialized task execution, or per-node pod scheduling.

<img width="1124" alt="image" src="https://github.com/user-attachments/assets/ac1ea09b-1811-487f-a8ec-af229ff5ba66">
<br>
<br>

> **Note:** The test container/image used in this setup is a public nginx container that runs unpriviledged. This is necessary per the restrictions listed above in the diagram. The container will natively start on a high port (8080) that does not require root to bind to. 

#### Workloads
Starting with the most commonly used method to deploy vK8s services, we will use a workload to deploy our example app. 
The workload will define our entire application, including the deployment, pods, service, load balancer and origin pool. 

Distributed Apps -> Applications -> Virtual K8s -> "Click on your vK8s name". 

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

### Advertise Options - Advertise on Internet (AoI) vs Advertise in Cluster (AiC)

**Advertise on Internet (AoI)** 

AoI does exactly what it sounds like. AoI preconfigures a service to expose the pods in vK8s, an origin pool referencing the service and finally a public, globally-redundant load balancer. 

AoI objects are created and managed by the workload. For example, you can not directly modify the load balancer or change or add anything outside of what is offered in the AoI config definition form. This is similar to the iApp feature on BIG-IP. When we choose AoI, we limit the amount of steps we need to take to deploy an app but we also are limited in terms of adding any additional functionality to the load balancer in support of the app.

**Advertise in Cluster (AiC)**

This setting is similar to AoI and while this automatically creates a vK8s service, it does not create an origin pool or load balancer. It will be up to the admin to define the origin pool, create the load balancer and deploy it to the Virtual Site appropriate to provide access to the service. For example, a load balancer running on a CE, publishing services to other clusters or to internal clients. 

The benefit of this model is the entire suite of XC security capabilities can be configured on the load balancer that weren't exposed in the AoI model. You can add WAAP, API Discovery, Bot mitigation and a whole bunch of other XC security services to protect the app. 

Either method (AoI or AiC) could be used to publish the app or service but which method you use will determine which post-deployment features are available for the service. We will explore the configuration and outcome of each method now. 

**AoI Method**

For Advertise Options, select "Advertise on Internet" from the dropdown and click the blue "Configure". 

<img width="514" alt="image" src="https://github.com/user-attachments/assets/e4d70a63-7316-4e2a-87e1-b7dfb10e9f2a">


Define the port information and then click the blue "Configure" under HTTP/HTTPS Load Balancer.

<img width="836" alt="image" src="https://github.com/user-attachments/assets/25423fbb-5416-4517-a8a6-08014c8131fb">

Configure the load balancer as shown below. 
<img width="831" alt="image" src="https://github.com/user-attachments/assets/0dcc11b8-815c-4244-8feb-e10add310602">

Click Apply, Apply, Apply, and finally Save and Exit. 

Within a few moments you should see your workload status 

<img width="1121" alt="image" src="https://github.com/user-attachments/assets/d1d1c004-ec91-4ae1-b775-3a022aaa7831">

Click on the "Deployments" tab and review the deployment deployed as part of the workload. 

<img width="897" alt="image" src="https://github.com/user-attachments/assets/75d8f8e0-1383-4f8a-9e09-5aa8feb1529f">

Click on the "Services" tab and review the services deployed as part of the workload. 

<img width="954" alt="image" src="https://github.com/user-attachments/assets/9baae205-b236-4560-b6d8-2138fedd002d">

Click on the "Replica Sets" tab and review the Replica Sets deployed as part of the workload. 

<img width="1124" alt="image" src="https://github.com/user-attachments/assets/fe850d59-0c42-4cf8-b5b2-b7cb6fe308ca">

Click on the "Pods" tab and review the Pods deployed as part of the workload. 

<img width="1120" alt="image" src="https://github.com/user-attachments/assets/4c5f4ffc-7f9a-488b-91c6-7950255386c7">

Click on "Home" and then click on Multi-cloud App Connect

<img width="485" alt="image" src="https://github.com/user-attachments/assets/d21f6abf-6b63-4b36-a13a-b5bb18f9c866">

From the left menu go to Manage -> Load Balancers -> HTTP Load Balancers

You will see the load balancer that was automatically created as part of the workload definitions. 

<img width="1122" alt="image" src="https://github.com/user-attachments/assets/0a142113-0862-4feb-b7e2-20df3f1286f2">

Click the "Actions" button and notice that there is no option to manage or edit the load balancer directly. To modify any aspect of the load balancer it would need to be initiated from the workload. 

<img width="1109" alt="image" src="https://github.com/user-attachments/assets/e6a93474-893d-40c4-a040-fac247fe9dc1">

#### Testing Access to the service
Click the little down arrow next to the load balancer 
<img width="1102" alt="image" src="https://github.com/user-attachments/assets/445ebb78-3fb4-4422-a8c7-724162c1c7c5">

Scroll down to around line 95 and find your IP address. 
<img width="346" alt="image" src="https://github.com/user-attachments/assets/ebeab53d-ee59-4e0e-bf9d-22b6bbdfb937">

Make a host file entry on your local machine to point nginx.example.com to the IP address in your config.

<img width="771" alt="image" src="https://github.com/user-attachments/assets/b66a9b93-2e54-4772-a6a3-fd6fc8ece100">

**AiC Method**

Testing the AiC method simply involves making a change to the workload and manually building an origin pool and load balancer. The real benefit of this method will be clear momentarily. 






#### Deployments

The test container/image used in this setup is a public nginx container that runs unpriviledged. This is necessary per the restrictions listed above in the diagram. The container will natively start on a high port (8080) that does not require root to bind to. 

**Imperative**
```
kubectl --kubeconfig=vK8s.kubeconfig create deployment nginx-imper --image=ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl
```
**Nodeport** (Need to test)
```
kubectl --kubeconfig=vK8s.kubeconfig expose deployment nginx-imper --type=NodePort --port=2000 --target-port=8080 --node-port=32000
```
**ClusterIP** (Need to test)
```
kubectl --kubeconfig=vK8s.kubeconfig expose deployment nginx-imper --type=ClusterIP --port=2000 --target-port=8080
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






## CE vK8s

## RE + CE vK8s


