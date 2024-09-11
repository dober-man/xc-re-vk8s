# Stateful Sets
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
