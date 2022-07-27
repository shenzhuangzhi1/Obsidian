# Download
Url for offline：[url](https://flink.apache.org/downloads.html)
# Docker Setup
## Getting Started
This _Getting Started_ section guides you through the local setup (on one machine, but in separate containers) of a Flink cluster using Docker containers.
### Introduction
[Docker](https://www.docker.com/) is a popular container runtime. There are official Docker images for Apache Flink available [on Docker Hub](https://hub.docker.com/_/flink). You can use the Docker images to deploy a _Session_ or _Application cluster_ on Docker. This page focuses on the setup of Flink on Docker and Docker Compose.

Deployment into managed containerized environments, such as [standalone Kubernetes](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/kubernetes/) or [native Kubernetes](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/native_kubernetes/), are described on separate pages.
### Starting a Session Cluster on Docker
A _Flink Session cluster_ can be used to run multiple jobs. Each job needs to be submitted to the cluster after the cluster has been deployed. To deploy a _Flink Session cluster_ with Docker, you need to start a JobManager container. To enable communication between the containers, we first set a required Flink configuration property and create a network:

```sh
$ FLINK_PROPERTIES="jobmanager.rpc.address: jobmanager"
$ docker network create flink-network
```

Then we launch the JobManager:

```sh
$ docker run \
    --rm \
    --name=jobmanager \
    --network flink-network \
    --publish 8081:8081 \
    --env FLINK_PROPERTIES="${FLINK_PROPERTIES}" \
    flink:1.15.1-scala_2.12-java11 jobmanager
```

and one or more TaskManager containers:

```sh
$ docker run \
    --rm \
    --name=taskmanager \
    --network flink-network \
    --env FLINK_PROPERTIES="${FLINK_PROPERTIES}" \
    flink:1.15.1-scala_2.12-java11 taskmanager
```

The web interface is now available at [localhost:8081](http://localhost:8081/).

Submission of a job is now possible like this (assuming you have a local distribution of Flink available):

```sh
$ ./bin/flink run ./examples/streaming/TopSpeedWindowing.jar
```

To shut down the cluster, either terminate (e.g. with `CTRL-C`) the JobManager and TaskManager processes, or use `docker ps` to identify and `docker stop` to terminate the containers.
## Deployment Modes

The Flink image contains a regular Flink distribution with its default configuration and a standard entry point script. You can run its entry point in the following modes:

-   [JobManager](https://nightlies.apache.org/flink/flink-docs-master/docs/concepts/glossary/#flink-jobmanager) for [a Session cluster](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#starting-a-session-cluster-on-docker)
-   [JobManager](https://nightlies.apache.org/flink/flink-docs-master/docs/concepts/glossary/#flink-jobmanager) for [an Application cluster](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#application-mode-on-docker)
-   [TaskManager](https://nightlies.apache.org/flink/flink-docs-master/docs/concepts/glossary/#flink-taskmanager) for any cluster

This allows you to deploy a standalone cluster (Session or Application Mode) in any containerised environment, for example:

-   manually in a local Docker setup,
-   [in a Kubernetes cluster](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/kubernetes/),
-   [with Docker Compose](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#flink-with-docker-compose),

Note [The native Kubernetes](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/native_kubernetes/) also runs the same image by default and deploys TaskManagers on demand so that you do not have to do it manually.

The next chapters describe how to start a single Flink Docker container for various purposes.

Once you’ve started Flink on Docker, you can access the Flink Web UI on [localhost:8081](http://localhost:8081/#/overview) or submit jobs like this `./bin/flink run ./examples/streaming/TopSpeedWindowing.jar`.

We recommend using [Docker Compose](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#flink-with-docker-compose) for deploying Flink in Session Mode to ease system configuration.