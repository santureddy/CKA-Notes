# CKA-Notes

This is my personal notes from the Kodekloud course <https://kodekloud.com/courses/certified-kubernetes-administrator-cka/>

## ETCD in kubernetes

- etcd datastore stores information regarding cluster such as nodes,pods,configs,secrets,accounts,roles,bindings,others.
- 2379 is the default port on which etcd listens.
- If we install etcd via kubeadm, then kubeadm deploys etcd as a pod which you can see in "kube-system" namespace. It's name would be "etcd-master"
- ETCDCTL is the CLI tool used to interact with ETCD.ETCDCTL can interact with ETCD Server using 2 API versions – Version 2 and Version 3.  By default its set to use Version 2. Each version has different sets of commands.

> kubectl get pods -n kube-system

- to list all keys stored by k8's use etcd control command line

> kubectl exec etcd-master -n kube-system etcdctl get / --prefix -keys-only

## kube-api-server
- Primary management component in K8's. kubectl utility reaches kube-apiserver for processing. It authenticates request and validates it. It then retrives data from etcd and posts back.
- To view api-server options when installed using kubeadm
> cat /etc/kubernetes/manifests/kube-apiserver.yaml
- To view api-server options when installed without kubeadm 
> cat /etc/systemd/system/kube-apiserver.service

## Kube Controller Manager

- Is a process that continuoesly monitors the state of various components within system and works towards bringing the whole system to a desired functioning state.
- It does that using kube-apiserver.

    - Node-controller : Responsible for monitoring status of nodes. Checks status of nodes every 5 sec so that it can monitor the node health. If it stops receiving heartbeat from node then node is marked unreachable ( waits 40 sec ). It waits for 5 min for node to comeback else it removes pods assigned on that node and assigns to healthy nodes.

    - Replication-controller : Responsible for monitoring status of replicasets and ensures desired number of pods are available all time.

    - There are many other controllers in K8's. You can see all of them in Kube-controller-manager. Ideally the file name is kube-controller-manager.service

## Kube scheduler

- Responsible for distributing/scheduling work/pods across multiple nodes. It looks for newly created pods and assigns them to nodes. It decides which pod should go into which node.
- Scheduler does this in two phases :
    - 1) Filter Nodes : Filter nodes that doesn't fit the profile for a pod. Example , Ndes which doesn't have sufficient cpu/memory.
    - 2) Rank Nodes : It ranks nodes on scale of 0-10 to decide which node would be best fit. It uses priority function to calculate the score.
    - Resoure requirements and limits 
    - Taints and tolerations.
    - Node selectors/ Affinity
- kube-scheduler.service is the config file when installed manually. If installed using kubeadm, it stores config in /etc/kubernetes/manifests/kube-scheduler.yaml
- To see the running process 
> ps -aux | grep kube-scheduler

## Kubelet 

- Kubelet in worker node registers node with K8's cluster.
- Creates PODS using container runtime engine.
- Monitors the state of the pods and Nodes and reports to kube-apiserver.
- **Kubelet needs to be manually installed** on worker node. It can't be installed using kubeadm.
- kubelet.service is the config file.

## Kube Proxy

- Within K8 cluster, every pod can reach every other pod. This is accomplished by deploying POD network solution ( an internal virtual network that spans across all nodes in clsuter ). Ideally we create **service** for each applications running in cluster withing pods which live outside the node and withing cluster. Service is a virtual component which lives in K8's memory.
- Kube-proxy is a process which runs in each node in k8's cluster. It looks out for new services and every time a new service is created it creates appropriate rules on each node to forward traffic to those services to the backend pods. One way it does this is using IP tables rules. It creates IP table rules in each node on the cluster.
- kube-proxy.service is the config file. Using kubeadm , it deploys kube-proxy as pods ( daemon sets to be specific ) on each node.

> kubectl get pods -n kubes-system | grep kube-proxy

> kubectl get daemonset -n kube-system