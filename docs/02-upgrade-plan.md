# Upgrade Plan

## Releases suported versions and skew policy

Kubernetes documentation describes currently supported components versions and version skew support policy
https://kubernetes.io/docs/setup/release/version-skew-policy/

The document refers to particular K8s versions. Let’s draw a generalized supported version skews principles.
So if the currently installed cluster version is x.y.z (e.g. 1.15.3) then the skew rules apply to the “y” element.


As a result allowed cluster components version skews are as follows:


### Control Plane
component | allowed versions | example
--- | --- | ---
kube-apiserver | (Ya..Ya+1) => Y | (1.16)
kube-controller-manager | (Y-1..Y) | (1.15..1.16)
kube-scheduler | (Y-1..Y) | (1.15..1.16)
cloud-controller-manager | (Y-1..Y) | (1.15..1.16)


### Worker Node
component | allowed versions | example
--- | --- | ---
kubelet: | (Y-2..Y) | (1.14..1.16)
kube-proxy | (Y-2..Y) | (1.14..1.16)


### Management
component | allowed versions | example
--- | --- | ---
kubectl | (Y-1..Y+1) | (1.15..1.17)

From above we can take the following conclusions:
- kube-apiserver acts as a base version guidance for the other components (Y)
- kube-apiserver can vary one minor version between nodes in a cluster
- kube-controller, kube-scheduler, cloud-controller-manager can not be newer than kube-apiserver within node
- kubectl is the only component which can be one minor version higher than kube-apiserver component

## Upgrade plan is simple: 
1. **upgrade control plane nodes one by one**
2. **upgrade worker nodes**
3. **upgrade kubectl on the management machine**


> Disclaimer: because KTHW is a lab environment backup and roll-back steps are out of scope of this exercise.

 Next: [Control Plane upgrade](03-control-plane-upgrade.md)