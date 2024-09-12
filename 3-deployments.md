# Deployments

When to use a Deployment?

Deployments are best suited for stateless applications where:
* The identity of individual pods doesn't matter.
* Pods are ephemeral and can be replaced at any time without issues (e.g., web servers serving static content, microservices without data persistence).
* There's no need for persistent storage or
* Storage can be shared across pods using something like a shared volume (PVC).

Deployments require a more fundamental understanding of the various components of a K8s environment and will require more manual stitching of the various components than deploying via workloads.  

## Imperative Deployment and Expose Service
Using the downloaded kubeconfig file from earlier, imperatively create a deployment named nginx-imper and expose. THis is a quick and easy way to test that everything is working as expected. 

In this example my kubeconfig file was named **"vK8s.kubeconfig"**. This command imperatively creates a deployment called "nginx-imper" using the example nginx app container on port 8080. It then exposes the nginx service as ClusterIP on port 2000. Notice there is no reference to a YAML file in this command other than the kubeconfig which is auth. 

Run this command from your local client where you have kubectl installed. I installed kubectl and ran this from my Macbook.

```
kubectl --kubeconfig=vK8s.kubeconfig create deployment nginx-imper --image=ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl && kubectl --kubeconfig=vK8s.kubeconfig expose deployment nginx-imper --type=ClusterIP --port=2000 --target-port=8080
```
> **Note:** - you may see a bunch of **"96483 memcache.go:287 couldn't get resource list for X"** status messages. This happens if features are restricted or disabled and can safely be ignored. 

Click on the "Deployments" tab and click "Refresh". You should see your new deployment running alongside of the deployment already created from the workload earlier. 

<img width="1116" alt="image" src="https://github.com/user-attachments/assets/01df9b7e-9eb5-41f4-8611-b2e8c865b0ea">

Click on the "Services" tab and click "Refresh". You should see your new service running alongside of the service already created from the workload earlier and default kubeapi service. 

<img width="1121" alt="image" src="https://github.com/user-attachments/assets/b54277e9-22f3-4726-b7e4-551b7f0eaf27">

You could now create a load balancer and origin pool and reference this service like we did in the previous workloads lab - AiC section. We will do this momentarily. 

## Declarative Deployment
For declarative, you could either paste the YAML into the XC Console form under "Deployments" or save the YAML in local files on your client and reference with kubectl. We will show both examples below. 

### Declarative via XC Console
Navigate to the "Deployments" tab and click "Add Deployment". You should still see deployments running from the previous workload and imperative exercises. 

<img width="1130" alt="image" src="https://github.com/user-attachments/assets/cd2dc16c-2a67-448a-8779-d1acd5f4b1ee">

Paste in the metadata on **line 2** underneath "Kind: Deployment" and click "Save".

```
metadata:
  name: nginx-declar
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-declar
  template:
    metadata:
      labels:
        app: nginx-declar
    spec:
      containers:
      - name: nginx-declar
        image: ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl
        ports:
        - containerPort: 8080
        resources:
          limits:
            cpu: "500m"
            memory: "150Mi"
          requests:
            cpu: "250m"
            memory: "100Mi"
```

<img width="524" alt="image" src="https://github.com/user-attachments/assets/466c4e47-2206-4429-9323-f06010959221">

Hit "Refresh" and within a few seconds you should see all your pods successfully deployed. 

<img width="1128" alt="image" src="https://github.com/user-attachments/assets/22f24b10-c9da-4e0e-8562-745e40bc1e11">

Now create a service to provide access to the pods. Click the "Services" tab and click "Add Service". 

<img width="1003" alt="image" src="https://github.com/user-attachments/assets/34509805-c5e4-4ead-8ec3-9c7788e3f36e">

Paste in the metadata on **line 2** underneath "Kind: Service" and click "Save".

**Declarative Service**
```
metadata:
  name: nginx-declar
spec:
  type: ClusterIP
  ports:
  - port: 2000
    targetPort: 8080
  selector:
    app: nginx-declar

```

<img width="544" alt="image" src="https://github.com/user-attachments/assets/c24d5260-cf40-4bbd-994c-ba058a3ea54a">




### Quotas

why dont declarative pods come up? resource limits per vk8s - delete imperative 
cover quotas. CPU Limits refer to the maximum amount of CPU resources that a container or workload can use.
CPU Requests are the guaranteed amount of CPU that the workload will be allocated to run.


#### Declarative with kubectl

```
kind: Deployment
metadata:
  name: nginx-declar
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-declar
  template:
    metadata:
      labels:
        app: nginx-declar
    spec:
      containers:
      - name: nginx-declar
        image: ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl
        ports:
        - containerPort: 8080
        resources:
          limits:
            cpu: "500m"
            memory: "150Mi"
          requests:
            cpu: "250m"
            memory: "100Mi"
```






### Publish Declarative service
modify existing k8s svc pool - test. 

**This concludes the Deployments learning section. Continue to 4-stateful-sets.**


