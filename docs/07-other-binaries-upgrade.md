# Upgrade other worker node binaries

While deploying and bootstrapping KTHW cluster you have installed couple of additional worker nodes binaries
They shall be upgraded node by node

#### Exict pods from worker node 
Before executing worker node upgrade we shall make sure its pods go to another node and mark it as temporarily unschedulable.

 From management machine run drain command to safely evict pods from the first worker node
 ```
 kubectl drain worker-0
```
#### Stop worker node 
Stop worker nodes K8s services
```
 sudo systemctl stop kubelet kube-proxy
 sudo systemctl status kubelet kube-proxy
```

Stop worker nodes containerd service
```
 sudo systemctl stop containerd
 sudo systemctl status containerd
```

#### Download binaries with newer version and deploy
```
CRI_VER=v1.18.0
RUNC_VER=v1.0.0-rc10
CNI_VER=v0.8.6
CNTD_VER=1.3.4

 wget -q --show-progress --https-only --timestamping \
  https://github.com/kubernetes-sigs/cri-tools/releases/download/${CRI_VER}/crictl-${CRI_VER}-linux-amd64.tar.gz \
  https://github.com/opencontainers/runc/releases/download/${RUNC_VER}runc.amd64 \
  https://github.com/containernetworking/plugins/releases/download/${CNI_VER}/cni-plugins-linux-amd64-v0.8.6.tgz \
  https://github.com/containerd/containerd/releases/download/v${CNTD_VER}/containerd-${CNTD_VER}.linux-amd64.tar.gz

{
  tar -xvf crictl-${CRI_VER}-linux-amd64.tar.gz
  tar -xvf containerd-${CNTD_VER}.linux-amd64.tar.gz -C containerd
  sudo tar -xvf cni-plugins-linux-amd64-${CNI_VER}.tgz -C /opt/cni/bin/
  sudo mv runc.amd64 runc
  chmod +x crictl runc 
  sudo mv crictl runc /usr/local/bin/
  sudo mv containerd/bin/* /bin/
}
```
### Start worker nodes component services

```
 sudo systemctl start containerd
 sudo systemctl status containerd
```

```
 sudo systemctl start kubelet kube-proxy
 sudo systemctl status kubelet kube-proxy
```

Uncordon upgraded worker node to allow pods to be scheduled on the upgraded node
```
 kubectl uncordon worker-0
```

Repeat this process on the remaining worker nodes


