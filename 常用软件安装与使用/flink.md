# Download
Url for offline：[url](https://flink.apache.org/downloads.html)
# deploy
## Docker

A _Flink Session cluster_ can be used to run multiple jobs. Each job needs to be submitted to the cluster after the cluster has been deployed. To deploy a _Flink Session cluster_ with Docker, you need to start a JobManager container. To enable communication between the containers, we first set a required Flink configuration property and create a network:

```sh
FLINK_PROPERTIES="jobmanager.rpc.address: jobmanager"
docker network create flink-network
```

