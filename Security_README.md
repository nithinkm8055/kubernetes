## Security Primitives
-----------------------

The first level of defense begins at kube-apiserver  
  
### Authentication

There are many ways :  
1) Static Password file
2) Token file  
3) Certificates  
4) Identity Services  


```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --basic-auth-file=/tmp/users/user-details.csv
    image: k8s.gcr.io/kube-apiserver-amd64:v1.11.3
    name: kube-apiserver
    volumeMounts:
    - mountPath: /tmp/users
      name: usr-details
      readOnly: true
  volumes:
  - hostPath:
      path: /tmp/users
      type: DirectoryOrCreate
    name: usr-details
```


## Cerificate signing
---------------------
  
openssl genrsa -out ca.key 1024 # Generate a private key   
openssl rsa -in ca.key  -pubout > pubkey.pem  


> CA for kubernetes cluster  
openssl genrsa -out ca.key 1024 # Generate a private key  
openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr # create a certificate signing request  
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt  

openssl x509 -in server.crt -text -noout # view certificate contents     
  
    
> Create and sign certificate for kube admin using the CA and key created before   
openssl x509 -req -in admin.csr -CAkey ca.key -CA ca.crt -out admin.crt  

> For kube api server consider all the domain aliases  
kubernetes  
kubernetes.default  
kubernetes.default.svc  
kubernetes.default.svc.cluster.local  

To create this, create a openssl.cnf file    
[alt_names]  
DNS.1 = kubernetes   
DNS.2 = kubernetes.default   
DNS.3 = kubernetes.default.svc   
IP.1  =   10.96.0.1  
IP.2  = 172.17.0.87  
  
>  openssl x509 -in /var/lib/minikube/certs/apiserver.crt -text -noout  # check details of a certificate  
  
ADMINS can approve csr requets as below  
kubectl get csr # get pending requests  
kubectl certificate approve <name>  

kubectl get csr nithin -o yaml  

## KubeConfig  
  
To use that context, run the command: kubectl config --kubeconfig=/root/my-kube-config use-context research  
To know the current context, run the command: kubectl config --kubeconfig=/root/my-kube-config current-context  

## RBAC  
kubectl auth can-i create deployments  
kubectl auth can-i delete replicasets 
  
> kubectl auth can-i create pods --as dev-user # where dev-user is a normal user   
  
> kubectl create role developer --resource=pods --namespace=default --verb=list,create --resource=pods  
  
> kubectl create rolebinding developer-binding --namespace=default --role=developer --user=dev-user  

## Image Security  
> kubectl create secret dockerregistry regcred \  
--docker-server=   \  
--docker-username=   \  
--docker-password=   \  
--docker-email=    
   
## Security Context
Used to link a specific user/group to a pod or container.  
Also can be used to add capabilities.  
  
## Network Policy
Flannel does not support network policy  
  
Supported ones:  
> kube-router  
> Calico  
> Weave-net  
> Romana  



## Commands
> kubectl api-resources --namespaced=true  
kubectl api-resources --namespaced=false  