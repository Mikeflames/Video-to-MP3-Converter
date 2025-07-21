# Auth Microservice – Kubernetes Deployment

This project contains Kubernetes manifests to deploy the `auth` microservice. The setup includes configuration, secrets, service exposure, and deployment specifications.

---

## 📁 Files Overview

### `auth-deploy.yaml`
Defines a **Deployment** resource to manage the `auth` microservice.

- Specifies:
  - Docker image (`yourname/auth-service`)
  - Number of replicas
  - Pod selectors and labels
  - Environment variable setup
  - Exposed container port

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: auth
  template:
    metadata:
      labels:
        app: auth
    spec:
      containers:
        - name: auth
          image: yourname/auth-service
          ports:
            - containerPort: 5000
configmap.yml
Defines a ConfigMap to provide non-sensitive configuration to the pods.

Stores:

Hostnames

Environment tags

API URLs

yaml
Copy
Edit
apiVersion: v1
kind: ConfigMap
metadata:
  name: auth-config
data:
  DB_HOST: mysql
  ENV: production
secret.yaml
Defines a Secret to store sensitive data securely.

Stores:

Passwords

API keys

Tokens

⚠️ All values are base64 encoded.

yaml
Copy
Edit
apiVersion: v1
kind: Secret
metadata:
  name: auth-secret
type: Opaque
data:
  DB_PASSWORD: cGFzc3dvcmQ=   # base64 of 'password'
To decode a secret manually:

bash
Copy
Edit
echo cGFzc3dvcmQ= | base64 --decode
service.yaml
Defines a Service to expose the auth microservice internally or externally.

Types:

ClusterIP (default – internal communication)

NodePort (node-level external access)

LoadBalancer (cloud-provider-managed public access)

yaml
Copy
Edit
apiVersion: v1
kind: Service
metadata:
  name: auth-service
spec:
  selector:
    app: auth
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
  type: ClusterIP
🚀 Usage
Deploy all resources:
bash
Copy
Edit
kubectl apply -f configmap.yml
kubectl apply -f secret.yaml
kubectl apply -f auth-deploy.yaml
kubectl apply -f service.yaml
View resources:
bash
Copy
Edit
kubectl get pods
kubectl get services
kubectl get configmaps
kubectl get secrets
📦 Summary
File	Resource Type	Description
auth-deploy.yaml	Deployment	Runs and manages auth app pods
configmap.yml	ConfigMap	Supplies non-sensitive environment data
secret.yaml	Secret	Securely stores sensitive credentials
service.yaml	Service	Exposes the auth service to the network

📘 Notes
Ensure your Docker image is built and pushed to a container registry accessible by your cluster (e.g., Docker Hub, ECR, GCR).

Use Secrets for database passwords or API tokens, not ConfigMap.

You can debug with:

bash
Copy
Edit
kubectl logs <pod-name>
kubectl describe pod <pod-name>
Combine these with other services (like DB, frontend) using Docker Compose or Helm if needed.