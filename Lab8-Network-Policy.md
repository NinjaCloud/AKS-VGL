## Network Policy

### Task 1: Check the network connectivity between pods in different namespaces
Create 2 namespaces
```
kubectl create ns ns1
```
```
kubectl create ns ns2
```
Create pod in first namespace
```
kubectl -n ns1 run ns1-pod --image nginx 
```
Create pod in second namespace
```
kubectl -n ns2 run ns2-pod --image nginx 
```
Enter the pod in first namespace  and check its connectivity with a pod in second namespace
```
kubectl exec -it -n ns1 ns1-pod -- sh
```
Ping with the IP address of second pod
```
apt update && apt install iputils-ping
```
```
ping -c 3 <Ip Address of second pod>
```
![image](https://github.com/user-attachments/assets/6b9510b8-174a-4aed-b948-5ea6ad9500ff)

```
exit
```
Enter the pod in second namespace  and check its connectivity with a pod in first namespace
```
kubectl -n ns2 exec -it ns2-pod -- sh
```
Ping with the IP address of first pod
```
apt update && apt install iputils-ping
```
```
ping -c 3 <Ip Address of first pod>
```
![image](https://github.com/user-attachments/assets/4418c9bc-0c80-4f0c-bd17-ddf7be86971d)

```
exit
```

### Task 2: Ingress Network policy with Pod labels 

Create a nginx pod and service with labels role=backend
```
kubectl run backend --image nginx -l role=backend
```
```
kubectl expose po backend --port 80 
```
```
kubectl get pod
```
```
kubectl get svc
```
Create a new busybox pod and verify that it can access the backend service.
Then, exit out of the container
```
kubectl run --rm -it --image=busybox net-policy 
```
```
wget -qO- -T3 http://backend   #curl http://backend
```
```
exit
```
Create a network policy which uses labels to deny all ingress traffic
```
vi ingress-policy.yml
```
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress: []
    
```
```
kubectl apply -f ingress-policy.yml
```
```
kubectl get networkpolicies
```
Create a new busybox pod again and verify that it cannot access the backend service
```
kubectl run --rm -it --image=busybox net-policy
```
```
wget -qO- -T3 http://backend
```
It will show timeout
![image](https://github.com/user-attachments/assets/e6b5610f-e194-42ca-812d-15d6af420018)

```
exit
```
Modify the network policy to allow traffic with matching Pod labels 
Inspect the network policy and notice the selectors
```
vi ingress-policy.yml
```
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          role: frontend		  
```
Before applying this yaml describe networkpolicies to check rule
```
kubectl replace -f ingress-policy.yml --force
```
```
kubectl describe networkpolicies backend-policy
```
Run the busybox pod again and verify that it is able to access backend service.
Notice the labels on the Pod. Inspect the network policy and notice the selectors
```
kubectl run --rm -it --image=busybox --labels role=frontend net-policy
```
```
wget -qO- -T3 http://backend
```
![image](https://github.com/user-attachments/assets/032a6002-7894-4881-bbbe-0a5e9d341647)

```
exit
```
Run the busybox pod again without labels and notice that the backend service is not accessible any more.
```
kubectl run --rm -it --image=busybox net-policy 
```
```
wget -qO- -T3 http://backend
```
![image](https://github.com/user-attachments/assets/b8adaa3d-45cf-44d7-b042-7adfbab0e884)

```
exit
```

### Task 3: Egress Policy
Create a pod and check the Egress Rules applied to it by default
```
kubectl run pod1 --image nginx
```
```
kubectl exec -it pod1 -- bash
```
```
curl https://8.8.8.8
```
```
curl https://yahoo.com
```
![image](https://github.com/user-attachments/assets/160cd45c-70a1-4322-9f1e-1e4d85a5cf47)

```
exit
```
Create an Egress Policy
```
vi egress-policy.yaml
```
Create a Kubernetes Network Policy manifest file to define egress rules.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-outbound
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 8.8.8.8/32  # Example IP address
```
Apply the network policy 
```
kubectl apply -f egress-policy.yaml
```
Verify that the egress policy has been applied to your pods:
```
kubectl get networkpolicies
```
```
kubectl describe networkpolicies
```
Enter the Pod1 and check if the network policy has been applied to it
```
kubectl exec -it pod1 -- bash
```
```
curl https://8.8.8.8
```
It can access the IP as it is enabled by the Egress Policy
```
curl https://yahoo.com
```
It is not able to access other than mentioned in the EgressPolicy
![image](https://github.com/user-attachments/assets/83c26cf8-90c8-42b8-8fa6-8fde30602804)

