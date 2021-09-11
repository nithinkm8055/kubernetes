## Kubernetes for Beginners

* Kubernetes is a container orchestration technology designed and created by Google Inc.
* It is developed in _Go_ Programming language.

### Command line utilities

**kubectl**  
    A command line utility that can be used to create/run/manage kubernetes related objects.  
    Installation: https://kubernetes.io/docs/tasks/tools/

### High level Architecture

### Setup Environment


### Specific terms

#### Pods  
> kubectl run nginx-pod ---image nginx  
> kubectl get pods  
> kubectl create -f pod.yaml  
> kubectl describe pod <pod_name>  
> kubectl edit pod <pod_name>  
> kubectl get pod --selector app=App1  
> kubectl get pod <pod name> --show-labels


#### ReplicaSets  

> kubectl get replicasets  
> kubectl create -f replicaset.yaml  
> kubectl describe replicaset <replicaset_name>  
> kubectl edit replicaset <replicaset_name>  
> kubectl replace -f <replicaset_name>  
> kubectl scale replicaset <replicaset_name> --replicas=<number>  


#### Deployments  

Rolling update is the default update strategy  
Another update strategy is Recreate  
> kubectl get deployments  
> kubectl create -f deployment.yaml  
> kubectl describe deployment <deployment_name>  
> kubectl edit deployment <deployment_name>  
> kubectl set image deployment <deployment_name> <imagename>:<version>  
> kubectl rollout status deployment.apps/<deployment_name>  
deployment "<deployment_name>" successfully rolled out  
> kubectl rollout history deployment.apps/<deployment_name>  
> kubectl rollout undo deployment.apps/<deployment_name>  
> kubectl scale deployment <replicaset_name> --deployment=<number>  
> kubectl expose deployment nginx --port 80  

#### Namespace
> kubectl create -f namespace-definition.yaml  
> kubectl create namespace dev  

> To set another namepace as default 
kubectl config set-context $(kubectl config current-context) --namespace=dev  
kubectl config use-context user@cluster

> kubectl get pods --all-namespaces  
or  
kubectl get pods -A  

#### Taints and Tolerations
* Taints are placed on nodes and tolerations are applied to pods  
kubectl taint nodes node-name key=value:taint-effect  
taint-effect : { NoSchedule | PreferNoSchedule | NoExecute }  

* Untaint a node  
kubectl taint nodes node-name key=value:taint-effect-  
-> Notice the - symbol at the end which removes the taint

#### Cluster Networking

Different Solutions
> Calico  
> flannel  
> cilium  
> Cisco  
> weavenet  
> vmware NSXT  

#### Commands
> kubectl get pods -n kube-system  
> kubectl exec etcd-minikube -n kube-system -- etcdctl get --prefix --keys-only  
> export ETCDCTL_API=3  
> kubectl exec etcd-minikube -n kube-system -- sh -c "ETCDCTL_API=3 etcdctl get --prefix --keys-only --limit=10 --cacert /etc/kubernetes/pki/etcd/ca.crt --cert /etc/kubernetes/pki/etcd/server.crt  --key /etc/kubernetes/pki/etcd/server.key"   

> To get all options available for a pod  
* kubectl explain pod --recursive  
to get five lines below tolerations from above output  
* kubectl explain pod --recursive | grep -A5 tolerations  
> kubectl get events  
> View logs of a pod   
kubectl logs -f my-scheduler --namespace=kube-system  
kubectl logs -f my-scheduler -c <container name> --namespace=kube-system  (if there are multiple containers)  
 
#### Control plane components
> kube-apiserver   
> kubelet  
> kube-proxy  
> kube-controller-manager  
> kube-scheduler  
> etcd  

#### Scheduling
> curl --header "Content-Type:application/json" --data "" --request POST http://$SERVER/api/v1/namespaces/default/pods/$PODNAME/binding/

#### Logging and Monitoring
After installing the metrics-server you can use below command to inspect cpu/mem for pod / node  
> kubectl top node  
> kubectl top pod  
> View logs of a pod   
kubectl logs -f my-scheduler --namespace=kube-system  (-f helps to stream the live logs)  
  
if a pod has multiple containers within a pod, then specify the container name as well
kubectl logs -f pod_name container_name  

#### ConfigMaps / Env Variables / Secrets

kubectl create configmap <config_name> --from-literal=<key>=<value>  
kubectl create configmap <config_name> --from-file=<path-to-file>  
  
kubectl create secret generic <secret-name> --from-literal=<name>=<value>  
kubectl create secret generic <secret-name> --from-file=<path-to-file>  
  
#### Nodes
> kubectl label nodes node1 label-key=label-value  
> Drain nodes -> moves all pods outside of node  
kubectl drain node01 --ignore-daemonsets  

kubectl cordon node01 --> makes node unschedulable  
kubectl uncordon node01 --> make node schedulable again  

#### Usage and storage
https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-controller-manager  
https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-apiserver  
https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kubelet  
https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-proxy  
https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler  

#### Versions

kube-apiserver - X  
kube-scheduler | controller-manager --> X-1, X  
kubelet | kube-proxy --> X-2, X-1, X  
  
kubectl  --> X+1, X, X-1  

kubernetes supports only upto 3 versions at a time.

#### Cluster Upgrade Procedure

apt-get upgrade -y kubeadm=1.19.0-00  
with kubeadm
 --> check latest stable version --> kubeadm upgrade plan --> kubeadm upgrade apply v1.19.0  
* drain the node  (kubectl drain)  
* Upgrade kubeadm  on node  
* Upgrade kubelet and kubectl  
* uncordon the node after verification  
  
  
#### Backups
Method 1: kubectl get all -A -o yaml --> this should provide backup for all resources in the cluster.  
Method 2:  
etcdctl snapshot save snapshot.db  (specify --cert, --cacert, --key arguments)  
etcdctl snapshot status snapshot.db  
etcdctl snapshot restore snapshot.db --data-dir /var/lib/etcd-from-backup   (Further update the configuration of etcd yaml to point to this new backup directory to do a restore)  
  
Use the etcdctl snapshot save command. You will have to make use of additional flags to connect to the ETCD server.  
--endpoints: Optional Flag, points to the address where ETCD is running (127.0.0.1:2379)  
--cacert: Mandatory Flag (Absolute Path to the CA certificate file)  
--cert: Mandatory Flag (Absolute Path to the Server certificate file)  
--key:Mandatory Flag (Absolute Path to the Key file)  

#### Storages
> Check Volume mounts on pods  
> Storage Classes  
> PersistentVolume and PersistentVolumeClaims 


### Notes

> To access a service in another namespace, use hostname as:  
<service_name>.<namespace_name>.svc.cluster.local  

> Create yaml file using command:  
* kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > deployment-definition.yaml  
* kubectl run nginx --image nginx --dry-run=client -o yaml > pod-definition.yml  
* Create a Service named nginx of type NodePort to expose pod nginx's port 80 on port 30080 on the nodes:  
kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service --dry-run=client -o yaml  

* Kubernetes provides additional support to check the health of applications running within PODs and take necessary actions through Liveness and Readiness Probes  
* Get pods with specific labels  
kubectl get pods -l name=payroll  
kubectl get pods --show-labels  

* ALL COMMANDS: `https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands`

* Quoram (Minimun number of nodes in cluster to stay healthy) -> `N/2 + 1` , where N is total number of nodes  
  
* Verify etcd command works:  
ETCDCTL_API=3 etcdctl --cacert=/etc/kubernetes/pki/etcd/ca.crt --key=/etc/kubernetes/pki/etcd/server.key --cert=/etc/kubernetes/pki/etcd/server.crt member list  
  
Snapshot status to a table  
ETCDCTL_API=3 etcdctl snapshot status /opt/etcd-backup.db --cacert=/etc/kubernetes/pki/etcd/ca.crt --key=/etc/kubernetes/pki/etcd/server.key --cert=/etc/kubernetes/pki/etcd/server.crt -w table   
  
* Debug kubeconfig  
kubectl cluster-info --kubeconfig=<path to kubeconfig>

  
* Only a scheduler takes taints/tolerations/affinity into account. This means, when you do a manual schedule, the taints on a node are no longer taken into effect.    
