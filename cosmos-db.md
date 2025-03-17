### **What is Azure Cosmos DB?**
Azure Cosmos DB is a globally distributed, multi-model NoSQL database service offered by Microsoft Azure. It provides high availability, low latency, and automatic scaling capabilities, making it ideal for applications that require massive throughput and global distribution.

### **Key Features of Azure Cosmos DB:**
- **Multi-Model Support**: Supports multiple data models, including document (MongoDB, SQL API), key-value (Table API), graph (Gremlin API), and column-family (Cassandra API).
- **Global Distribution**: Enables multi-region replication with low-latency access.
- **Scalability**: Supports automatic scaling of read/write throughput.
- **Fully Managed**: No need to manage infrastructure, patching, or updates.
- **Multi-API Support**: SQL (Core) API, MongoDB API, Cassandra API, Gremlin API, and Table API.

---

## **Deploying Cosmos DB API on an AKS Cluster**
To integrate Azure Cosmos DB with an **Azure Kubernetes Service (AKS)** cluster, follow these steps:

### **1. Create an Azure Cosmos DB Instance**
1. **Log in to Azure Portal**.
2. **Create a new Azure Cosmos DB account**:
   - Go to **Azure Cosmos DB** → **Create**.
   - Choose an API (e.g., SQL API, MongoDB API, Cassandra API).
   - Configure resource group, account name, region, and throughput settings.
   - Click **Review + Create** → **Create**.

---

### **2. Set Up an AKS Cluster**
If you don’t have an AKS cluster, create one using the Azure CLI:

```bash
az aks create --resource-group myResourceGroup \
  --name myAKSCluster --node-count 2 \
  --enable-managed-identity \
  --generate-ssh-keys
```

Retrieve the AKS credentials:
```bash
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

---

### **3. Create a Kubernetes Secret for Cosmos DB Credentials**
Retrieve the **Cosmos DB connection string** from the **Azure Portal**:

```bash
COSMOS_DB_CONN_STRING=$(az cosmosdb keys list \
  --name myCosmosDBAccount \
  --resource-group myResourceGroup \
  --type connection-strings \
  --query "connectionStrings[0].connectionString" --output tsv)
```

Create a Kubernetes secret:

```bash
kubectl create secret generic cosmosdb-secret \
  --from-literal=cosmosdb-uri="$COSMOS_DB_CONN_STRING"
```

---

### **4. Deploy an Application that Uses Cosmos DB**
Create a Kubernetes **Deployment YAML file** (`cosmos-app.yaml`):

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: cosmos-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: cosmos-app
  template:
    metadata:
      labels:
        app: cosmos-app
    spec:
      containers:
        - name: cosmos-container
          image: myregistry.azurecr.io/cosmos-app:v1
          env:
            - name: COSMOS_DB_URI
              valueFrom:
                secretKeyRef:
                  name: cosmosdb-secret
                  key: cosmosdb-uri
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: cosmos-service
spec:
  selector:
    app: cosmos-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

Apply the deployment:

```bash
kubectl apply -f cosmos-app.yaml
```

---

### **5. Verify the Deployment**
Check if the pods are running:

```bash
kubectl get pods
```

Get the service external IP:

```bash
kubectl get service cosmos-service
```

Once the service is running, you can use the **public IP** to access the deployed application.

---

### **Conclusion**
By following these steps, you have successfully deployed an **Azure Cosmos DB API** on an **AKS cluster**. Your application running inside the AKS cluster can now interact with Cosmos DB securely using environment variables and Kubernetes secrets. 🚀
