# **Lab: Understanding Security Context in Kubernetes**

## **Objective**
Learn to configure and verify pod and container-level security contexts in Kubernetes, focusing on user permissions, read-only root filesystems, and disabling privilege escalation.

---

## **Step 1: Prerequisites**
1. A running Kubernetes cluster.
2. `kubectl` is installed and configured to interact with the cluster.
3. Basic knowledge of YAML and Kubernetes Pods.

---

## **Step 2: Create a Pod with Security Context**

### **2.1 Define Pod-Level Security Context**

Create a file named `pod-security-context.yaml` with the following content:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-security-context
spec:
  securityContext:
    runAsUser: 1000       # Run the pod as user ID 1000
    runAsGroup: 3000      # Run the pod as group ID 3000
    fsGroup: 2000         # File system group
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    securityContext:
      allowPrivilegeEscalation: false  # Disable privilege escalation
      readOnlyRootFilesystem: true    # Make root filesystem read-only
```

### **2.2 Apply the YAML**

Deploy the pod:
```bash
kubectl apply -f pod-security-context.yaml
```

---

## **Step 3: Verify Pod-Level Security Context**

### **3.1 Check the Pod Status**

Ensure the pod is running:
```bash
kubectl get pod pod-security-context
```

### **3.2 Inspect the Pod’s Security Context**

Use the following command to view the applied security context:
```bash
kubectl get pod pod-security-context -o yaml
```
Look for the `securityContext` section in the output.

### **3.3 Verify User and Group IDs Inside the Container**

Access the running container:
```bash
kubectl exec -it pod-security-context -- bash
```

Run the following commands inside the container:
```bash
id   # Verify the user and group IDs
```

Expected output:
```
uid=1000 gid=3000 groups=2000
```

Try to write to the root filesystem:
```bash
touch /test-file
```

Expected result: Permission denied (due to `readOnlyRootFilesystem`).

Exit the container:
```bash
exit
```

---

## **Step 4: Create a Deployment with Container-Level Security Context**

### **4.1 Define Deployment YAML**

Create a file named `deployment-security-context.yaml` with the following content:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-security-context
spec:
  replicas: 2
  selector:
    matchLabels:
      app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      containers:
      - name: secure-nginx
        image: nginx:latest
        securityContext:
          runAsNonRoot: true             # Ensure the container does not run as root
          capabilities:
            drop:
            - ALL                       # Drop all Linux capabilities
```

### **4.2 Apply the YAML**

Deploy the resources:
```bash
kubectl apply -f deployment-security-context.yaml
```

---

## **Step 5: Verify Container-Level Security Context**

### **5.1 Check the Pods**

Ensure the deployment pods are running:
```bash
kubectl get pods -l app=secure-app
```

### **5.2 Inspect the Security Context**

Inspect one of the pods:
```bash
kubectl describe pod <pod-name>
```
Look under the `securityContext` section for container-level settings.

### **5.3 Verify the Non-Root User**

Access one of the running pods:
```bash
kubectl exec -it <pod-name> -- bash
```

Inside the container:
```bash
whoami  # Should not return "root"
```

Exit the container:
```bash
exit
```

---

## **Summary**

In this lab:
1. You created a pod with a pod-level security context specifying user/group IDs and filesystem restrictions.
2. You deployed a deployment with a container-level security context to ensure containers do not run as root and have minimal privileges.
3. You verified the security context configurations using `kubectl` and commands inside the containers.
