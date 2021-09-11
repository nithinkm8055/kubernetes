Parse json objects using JsonPath queries.  
  
Kubectl get nodes -o=jsonpath='<jsonpath_query>'  
'{range .items[*]}{.metadata.name}{"\t"}{.status.capacity.cpu}{"\n"}{end}'
  
kubectl get nodes -o=custom-columns=<COLUMN NAME>:<JSON PATH>,<COLUMN NAME2>:<JSON PATH2>
kubectl get nodes -o=custom-columns=NODE:.metadata.name # custom columns assume the query is to be iterated...  
kubectl get nodes --sort-by=.metadata.name  
  
kubectl config view --kubeconfig=my-kube-config -o jsonpath="{.users[*].name}" > /opt/outputs/users.txt  



