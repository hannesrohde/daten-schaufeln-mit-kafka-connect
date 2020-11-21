## Prerequisites

The setup for the live demo requires a microk8s node with addons `helm3`, `storage`, `registry` installed.

First we create a new namespace and install the [Strimzi](https://strimzi.io/) operator that provides Kafka and Kafka Connect:

    kubectl create namespace kafka-demo
    kubectl config set-context --current --namespace kafka-demo
    
    microk8s helm3 repo add strimzi https://strimzi.io/charts/
    microk8s helm3 install strimzi strimzi/strimzi-kafka-operator --namespace kafka-demo

## Setting up Kafka

For demonstration purposes, we deploy a Kafka broker with a single instance
and ephemeral storage. For production use we would of course run multiple
instances with proper storage!

    kubectl create -f 01-kafka-ephemeral-single.yaml
    kubectl wait kafka/my-cluster --for=condition=Ready --timeout=300s
    
## Setting up Kafka Connect

Next, we deploy a Kafka Connect instance

    kubectl create -f 02-kafka-connect-single-node.yaml
    
## Plugins

https://strimzi.io/docs/operators/latest/full/deploying.html#using-kafka-connect-with-plug-ins-str

Build extended container image

    buildah bud -t -t localhost:32000/kafka-with-plugins:0.20.0-kafka-2.6.0
