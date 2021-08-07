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
