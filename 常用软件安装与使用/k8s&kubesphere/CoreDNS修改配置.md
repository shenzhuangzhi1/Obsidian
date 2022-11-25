# 修改CoreDNS的ConfigMap
```shell
KUBE_EDITOR="vim" kubectl edit configmap coredns -n kube-system
```

在文件中加入下面的`hosts{}`文本块：
```shell

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
data:
  Corefile: |
    .:53 {
        errors
        health {
           lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           fallthrough in-addr.arpa ip6.arpa
           ttl 30
        }
        hosts {
			1.1.1.1 git.sheny.com

			fallthrough
        }
        prometheus :9153
        forward . /etc/resolv.conf {
           max_concurrent 1000
        }
        cache 30
        loop
        reload
        loadbalance
    }
kind: ConfigMap
metadata:
  creationTimestamp: "2022-05-11T06:06:02Z"
  name: coredns
  namespace: kube-system
  resourceVersion: "144059"
  uid: 6bd40daf-90f2-494d-a42d-a641839e4b7d
```
# 重启CoreDNS容器组
```shell
# 通过修改期望副本值来重启所有的实例
kubectl scale deployment coredns -n kube-system --replicas=0
kubectl scale deployment coredns -n kube-system --replicas=2
```