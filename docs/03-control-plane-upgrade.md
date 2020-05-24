# Control Plane upgrade

Let’s let’s log on to the first controller and verify our starting point:
```
 /usr/local/bin/kube-apiserver --version
 /usr/local/bin/kube-controller-manager --version
 /usr/local/bin/kube-scheduler --version

Kubernetes v1.15.3
```

Since kube-apiserver request are load balanced we can safely stop control plane’s components
```
 sudo systemctl stop kube-apiserver kube-controller-manager kube-scheduler
 sudo systemct status kube-apiserver kube-controller-manager kube-scheduler
```

Download kubernetes binaries with newer version and deploy 
```
 DOWNLOAD_URL=https://storage.googleapis.com/kubernetes-release/release
 KUBE_NEW=v1.16.10
 wget -q --show-progress --https-only --timestamping \
  "${DOWNLOAD_URL}/${KUBE_NEW}/bin/linux/amd64/kube-apiserver" \
  "${DOWNLOAD_URL}/${KUBE_NEW}/bin/linux/amd64/kube-controller-manager" \
  "${DOWNLOAD_URL}/${KUBE_NEW}/bin/linux/amd64/kube-scheduler"
```
```
chmod +x kube-apiserver kube-controller-manager kube-scheduler kubectl
sudo cp kube-apiserver kube-controller-manager kube-scheduler kubectl /usr/local/bin/
```

Start control plane services
```
 sudo systemctl start kube-apiserver kube-controller-manager kube-scheduler
 sudo systemctl status kube-apiserver kube-controller-manager kube-scheduler
```


Repeat the this process on the remaining control plane nodes

Now that you have got controller upgraded let’s verify cluster versions and health from management machine
```
 kubectl version --short

  Client Version: v1.15.3
  Server Version: v1.16.10
```
```
 kubectl get componentstatuses

   Controller-manager    Healthy   ok
   Scheduler            Healthy   ok
   Etcd-0            Healthy   {"health":"true"}
   Etcd-1            Healthy   {"health":"true"}
   Etcd-2            Healthy   {"health":"true"}
```

 Next: [Worker Nodes upgrade](04-worker-nodes-upgrade.md)