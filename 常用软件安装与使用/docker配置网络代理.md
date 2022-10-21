有时因为网络原因，比如公司 NAT，或其它啥的，需要使用代理。`Docker` 的代理配置，略显复杂，因为有三种场景。但基本原理都是一致的，都是利用 `Linux` 的 `http_proxy` 等环境变量。
## Dockerd 代理
在执行`docker pull`时，是由守护进程`dockerd`来执行。因此，代理需要配在`dockerd`的环境中。而这个环境，则是受`systemd`所管控，因此实际是`systemd`的配置。
```shell
vim /etc/systemd/system/docker.service
```

```shell
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:8080/" Environment="HTTPS_PROXY=http://proxy.example.com:8080/" Environment="NO_PROXY=localhost,127.0.0.1,.example.com"
```