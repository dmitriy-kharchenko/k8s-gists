# Create deployment with ConfigMap

## Creating ConfigMap

`configmap.yaml`
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-demo
data:
  index.html: |
    Hello, world!
```

## Creating Deployment

`deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
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
          image: nginx:1.19.3
          ports:
            - containerPort: 80
          volumeMounts:
            - name: html
              mountPath: "/usr/share/nginx/html"
              readOnly: true
      volumes:
        - name: html
          configMap:
            name: nginx-demo
            items:
              - key: "index.html"
                path: "index.html"
```

## Creating Service

`service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-app
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - port: 80
      nodePort: 30007
```

Get service address:

```bash
minikube service --url nginx-app
```

## Task with *

Replace NodePort service with LoadBalancer
