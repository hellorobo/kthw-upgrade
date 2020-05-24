# Worker nodes upgrade


Before executing worker node upgrade we shall make sure its pods go to another node and mark it as temporarily unschedulable.

 From management machine run drain command to safely evict pods from the first worker node
 ```
 kubectl drain worker-0
```

Now let’s logon to the first worker node and verify current component statuses
```
 /usr/local/bin/kubelet --version
 /usr/local/bin/kube-proxy --version

   Kubernetes v1.15.3
```

Stop worker nodes component services
```
 sudo systemctl stop kubelet kube-proxy
 sudo systemctl status kubelet kube-proxy
```

Download kubernetes binaries with newer version and deploy
```
 DOWNLOAD_URL=https://storage.googleapis.com/kubernetes-release/release
 KUBE_NEW=v1.16.10
 wget -q --show-progress --https-only --timestamping \
  "${DOWNLOAD_URL}/${KUBE_NEW}/bin/linux/amd64/kubelet" \
  "${DOWNLOAD_URL}/${KUBE_NEW}/bin/linux/amd64/kube-proxy"

 chmod +x kubelet kube-proxy
 sudo cp kubelet kube-proxy /usr/local/bin/
```

Start worker nodes component services
```
 sudo systemctl start kubelet kube-proxy
 sudo systemctl status kubelet kube-proxy
```

Uncordon upgraded worker node to allow pods to be scheduled on the upgraded node
```
 kubectl uncordon worker-0
```

Repeat this process on the remaining worker nodes
Now, kubectl shall report newer versions on the worker nodes
```
 kubectl get nodes
  NAME        STATUS   ROLES    AGE     VERSION
  worker-0    Ready    <none>   1d22h   v1.16.10
  worker-1    Ready    <none>   1d22h   v1.16.10
  worker-1    Ready    <none>   1d22h   v1.16.10
```

 Next: [Management Client upgrade](05-management-client-upgrade.md)
