# Kubernetes Web Application Deployment
This project contains Kubernetes manifests for deploying a simple **Nginx web application** using different controller types, including a `Pod`, `ReplicaSet`, and `Deployment`. It also exposes the application using a `Service` of type `NodePort`.

## Project Structure
This project includes the following resources:
- **Pod**: A single instance of the Nginx container.
- **ReplicaSet**: Ensures a specified number of Nginx pod replicas are running at all times.
- **Deployment**: Manages the ReplicaSet and supports rolling updates and rollbacks.
- **Service**: Exposes the Deployment to external traffic via NodePort on port `30080`.

## Resources

### Pod
```yaml
kind: Pod
metadata:
  name: web
spec:
  containers:
    - name: web
      image: nginx
      ports:
        - containerPort: 80
```

Creates a single standalone Nginx pod for testing purposes.

### ReplicaSet
```yaml
kind: ReplicaSet
metadata:
  name: web-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx
          ports:
            - containerPort: 80
```

Manages three replicas of the Nginx container. Automatically replaces any failed or terminated pods.

### Deployment
```yaml
kind: Deployment
metadata:
  name: web-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: web
          image: nginx
          ports:
            - containerPort: 80
```

Manages ReplicaSets and provides advanced features like rolling updates and rollback. Replaces the need to manage ReplicaSets directly.

### Service (NodePort)
```yaml
kind: Service
metadata:
  name: web-service
spec:
  selector:
    app: web
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
```

Exposes the web deployment outside the cluster on port `30080` of each node in the cluster.

## How to Use
1. **Apply all manifests**:
```bash
kubectl apply -f .
```

2. **Access the application**:
Visit `http://<NodeIP>:30080` in your browser.

> Replace `<NodeIP>` with the IP address of any node in your Kubernetes cluster.
