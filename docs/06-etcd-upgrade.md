# Etcd Upgrade

### Review the latest Etcd releases
[Etcd releases](https://github.com/etcd-io/etcd/releases/)

Similarly as K8s cluster components Ectd shall be upgraded by increasing at one level of minor version at the time

### Review cluster components version levels and health
 Display Etcd server version
```
sudo curl -s --cacert /etc/etcd/ca.pem --cert /etc/etcd/kubernetes.pem --key /etc/etcd/kubernetes-key.pem  https://localhost:2379/version

	{"etcdserver":"3.4.0","etcdcluster":"3.4.0"}
```

 Display etcdctl version
```
sudo ETCDCTL_API=3 etcdctl version

	etcdctl version: 3.4.0
	API version: 3.4
```

 Check health of Etcd cluster
```
sudo ETCDCTL_API=3 etcdctl endpoint health --endpoints=https://127.0.0.1:2379,https://172.31.23.75:2379,https://172.31.24.16:2379  --cacert=/etc/etcd/ca.pem  --cert=/etc/etcd/kubernetes.pem  --key=/etc/etcd/kubernetes-key.pem

	https://127.0.0.1:2379 is healthy: successfully committed proposal: took = 18.06295ms
	https://172.31.23.75:2379 is healthy: successfully committed proposal: took = 20.829068ms
	https://172.31.24.16:2379 is healthy: successfully committed proposal: took = 19.174699ms
```

### Take database snapshot from leader node
 Check who is etcd cluster leader

```
sudo curl -s --cacert /etc/etcd/ca.pem --cert /etc/etcd/kubernetes.pem --key /etc/etcd/kubernetes-key.pem  https://localhost:2379/metrics | grep etcd_server_is_leader

	# HELP etcd_server_is_leader Whether or not this member is a leader. 1 if is, 0 otherwise.
	# TYPE etcd_server_is_leader gauge
	etcd_server_is_leader 1
```

 Take database snapshot on the leader node
```
sudo ETCDCTL_API=3 etcdctl snapshot save backup.db --endpoints=https://localhost:2379  --cacert=/etc/etcd/ca.pem  --cert=/etc/etcd/kubernetes.pem  --key=/etc/etcd/kubernetes-key.pem


	{"level":"info","ts":1590237829.2706175,"caller":"snapshot/v3_snapshot.go:109","msg":"created temporary db 	file","path":"backup.db.part"}
	{"level":"warn","ts":"2020-05-23T12:43:49.283Z","caller":"clientv3/retry_interceptor.go:116","msg":"retry 	stream intercept"}
	{"level":"info","ts":1590237829.2838206,"caller":"snapshot/v3_snapshot.go:120","msg":"fetching 			snapshot","endpoint":"https://localhost:2379"}
	{"level":"info","ts":1590237829.4651377,"caller":"snapshot/v3_snapshot.go:133","msg":"fetched 			snapshot","endpoint":"https://localhost:2379","took":0.194457349}
	{"level":"info","ts":1590237829.4652557,"caller":"snapshot/v3_snapshot.go:142","msg":"saved","path":"backup.db"}
	Snapshot saved at backup.db
```

 Verify snapshot status
```
sudo ETCDCTL_API=3 etcdctl -w table  snapshot status backup.db --endpoints=https://localhost:2379  --cacert=/etc/etcd/ca.pem  --cert=/etc/etcd/kubernetes.pem  --key=/etc/etcd/kubernetes-key.pem

+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| 869a4134 |   267535 |        837 |     1.9 MB |
+----------+----------+------------+------------+
```

### Upgrade Etcd node
 Stop Etcd service
```
sudo systemctl stop etcd
```
Etcd service on counterpart nodes will complain about partner being unavailable
```
sudo journalctl -u etcd -l

23 13:09:23 controller-1 etcd[1370]: health check for peer b79c305f18239871 coud not connect: dial tcp 172.31.22.125:2380: connect: connection refused
```

 Download and deploy new verions of Etcd binaries
```
ETCD_VER=v3.4.9
DOWNLOAD_URL=https://github.com/etcd-io/etcd/releases/download
curl -L ${DOWNLOAD_URL}/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o etcd-${ETCD_VER}-linux-amd64.tar.gz

{
  tar -xvf etcd-${ETCD_VER}-linux-amd64.tar.gz
  sudo mv etcd-${ETCD_VER}-linux-amd64/etcd* /usr/local/bin/
}
```

Now etcd counterpart nodes will complain about higher version of the other node but the cluster will still be operational
```
sudo journalctl -u etcd -f

	May 23 13:35:44 controller-1 etcd[1370]: member b79c305f18239871 has a higher version 3.4.9
	May 23 13:35:44 controller-1 etcd[1370]: the local etcd version 3.4.0 is not up-to-date
```

### Repeat the same process on the remaining Ectd nodes, one by one
 When all nodes are upgraded verify etcdctl and server version
```
sudo ETCDCTL_API=3 etcdctl version
	etcdctl version: 3.4.9
	API version: 3.4
```
```
sudo systemctl start etcd

sudo curl -s --cacert /etc/etcd/ca.pem --cert /etc/etcd/kubernetes.pem --key /etc/etcd/kubernetes-key.pem  https://localhost:2379/version
	{"etcdserver":"3.4.9","etcdcluster":"3.4.0"}
```