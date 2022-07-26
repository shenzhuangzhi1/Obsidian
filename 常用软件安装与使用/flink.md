# Download
Url for offline：[url](https://flink.apache.org/downloads.html)
# deploy
## Docker

A _Flink Session cluster_ can be used to run multiple jobs. Each job needs to be submitted to the cluster after the cluster has been deployed. To deploy a _Flink Session cluster_ with Docker, you need to start a JobManager container. To enable communication between the containers, we first set a required Flink configuration property and create a network:

```sh
FLINK_PROPERTIES="jobmanager.rpc.address: jobmanager"
docker network create flink-network
```

Then we launch the JobManager:

```sh
docker run \
    --rm \
    --name=jobmanager \
    --network flink-network \
    --publish 8081:8081 \
    --env FLINK_PROPERTIES="${FLINK_PROPERTIES}" \
    flink:1.15.1-scala_2.12-java11 jobmanager
```

and one or more TaskManager containers:

```sh
docker run \
    --rm \
    --name=taskmanager \
    --network flink-network \
    --env FLINK_PROPERTIES="${FLINK_PROPERTIES}" \
    flink:1.15.1-scala_2.12-java11 taskmanager
```

The web interface is now available at [localhost:8081](http://localhost:8081/).