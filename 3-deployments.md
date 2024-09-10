#### Deployments

When to use a Deployment?

Stateless applications where:
* The identity of individual pods doesn't matter.
* Pods are ephemeral and can be replaced at any time without issues (e.g., web servers serving static content, microservices without data persistence).
* There's no need for persistent storage or storage can be shared across pods using something like a shared volume.

With Deployments you will need to do more manual stitching of the various components.  

The test container/image used in this setup is a public nginx container that runs unpriviledged. This is necessary per the restrictions when deploying on XC Regional Edges. The sample container will natively start on a high port (8080) that does not require root to bind to. 

**Imperative Deployment and Expose Service**
Using the downloaded kubeconfig file from earlier, imperatively create a deployment named nginx-imper and expose.

In this example my kubeconfig file was named **"vK8s.kubeconfig"**. This command creates a deployment called "nginx-imper" using the example nginx app container on port 8080. It then exposes the nginx service as ClusterIP on port 2000. 

```
kubectl --kubeconfig=vK8s.kubeconfig create deployment nginx-imper --image=ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl && kubectl --kubeconfig=vK8s.kubeconfig expose deployment nginx-imper --type=ClusterIP --port=2000 --target-port=8080
```

**Declarative Deployment**
```
apiVersion: apps/v1
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

**Declarative Service**
```
apiVersion: v1
kind: Service
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

### Quotas

why dont declarative pods come up? resource limits per vk8s - delete imperative 
cover quotas. CPU Limits refer to the maximum amount of CPU resources that a container or workload can use.
CPU Requests are the guaranteed amount of CPU that the workload will be allocated to run.

### Publish Declarative service
modify existing k8s svc pool - test. 

#### Stateful Sets
Use this for applications that have state and need persistent storage or a stable identity (e.g., databases, clustered apps).



pod names dont change

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: nginx-stateful
spec:
  serviceName: nginx
  replicas: 3
  selector:
    matchLabels:
      app: nginx-stateful
  template:
    metadata:
      labels:
        app: nginx-stateful
    spec:
      containers:
      - name: nginx-stateful
        image: ghcr.io/nginxinc/nginx-unprivileged:1.27.1-bookworm-perl
        ports:
        - containerPort: 8080  # Changed to port > 1024
        volumeMounts:
        - name: www-data
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www-data
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

