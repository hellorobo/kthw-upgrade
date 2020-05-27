# Management client upgrade

At this point our nodes are updated to a newer version. As mentioned in the planning section kubectl tool can stay at one version behind kube-apiserver, same or one version ahead of it. But in our case let’s make it even.

On management machine verify the current client version
```
 kubectl version --short --client

  Client Version: v1.15.3
```

Download kubectl binary with newer a version and deploy
```
 DOWNLOAD_URL=https://storage.googleapis.com/kubernetes-release/release
 KUBE_NEW=v1.16.10
 wget -q --show-progress --https-only --timestamping \
  "${DOWNLOAD_URL}/${KUBE_NEW}/bin/linux/amd64/kubectl"
 chmod +x ./kubectl
 cp ./kubectl /usr/local/bin/
```

Now when we check kubectl version we have finally all components at the same level
```
 kubectl version --short

  Client Version: v1.16.10
  Server Version: v1.16.10
```

Next: [Etcd upgrade](06-etcd-upgrade.md)