## Networking

### Basics


### Network Namespaces
* On a Linux host  
ip netns  
ip netns add red  
ip netns add blue   

  
   
* Establish connection between namespaces  
> Create a pipe (or a virtual cable)   
 ip link add veth-red type veth peer name veth-blue  

> Attach each interface to appropriate namespace  
 ip link set veth-red netns red    
 ip link set veth-blue netns blue  

> Attach IPs to the interfaces  
 ip -n red addr add 172.17.15.1/24 dev veth-red  
 ip -n blue addr add 172.17.15.2/24 dev veth-blue  

> Bring up the interfaces  
 ip -n red link set veth-red up  
 ip -n blue link set veth-blue up  
  
> Verify route table as below  :  
ip netns exec red route  
  
> To see the created namespaces  in Linux, navigate to  `/var/run/netns/`

------------------------------------
> Delete a link    
ip -n red link del veth-red # since the links are paired, deleting one removes the other as well  

-------------
  
> Route command   
ip route add 192.168.10.0/24 via 172.17.15.5  


### Services

Services are actually virtual objects. They do not exist physically or as any process on the server.  
  
Also a service once created spans the cluster.  
The service ip range is defined by the argument to _kube-apiserver_ with `--service-cluster-ip-range`  
This IP range should not conflict with ip assigned to pods  
  
When a service is created, a routing rule is configured by kube-proxy to its route table.  