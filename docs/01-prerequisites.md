# Prerequisites

Up and running Kubernetes cluster bootstrapped according to 
[Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)

We’ve got K8s cluster consisting of the following elements: 
- control plane nodes (controller-0, controller-1 … controller-n)
- worker nodes (worker-0, worker-1 ... worker-n)
- kube-apiserver load balancer (either as a managed service or compute instances) 
- cluster management instance with kubectl tool


We want to upgrade the cluster to a higher Kubernetes release
 https://github.com/kubernetes/kubernetes/releases 
 https://github.com/kubernetes/sig-release/blob/master/releases/patch-releases.m

 Next: [Upgrade Plan](02-upgrade-plan.md)