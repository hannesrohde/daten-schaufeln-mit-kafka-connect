## Prerequisites

The setup for the live demo requires a microk8s node with addons `dns`,`helm3`,`registry`,`storage` installed.

First we create a new namespace `kafka-demo` and install the [Strimzi](https://strimzi.io/) operator that provides Kafka and Kafka Connect as custom resources:

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

    kubectl create -f 02-kafka-connect-single-node-kafka.yaml

## Setting up kaf

We will use `kaf` to interact with Kafka from the command line.

Find out IP address and port of the Kafka broker:

    kubectl get service my-cluster-kafka-external-bootstrap
    > NAME                                  TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)           AGE
    > my-cluster-kafka-external-bootstrap   NodePort   10.152.183.116   <none>        30992:31620/TCP   4m21s

Configure `kaf` to use the Kafka broker:

    kaf config add-cluster local -b 10.152.183.116:30992
    kaf config select-cluster -c local

Verify `kaf` works by requesting the list of topics from Kafka:

    kaf topics
    > NAME                      PARTITIONS   REPLICAS   
    > __consumer_offsets        50           1          
    > connect-cluster-configs   1            1          
    > connect-cluster-offsets   25           1          
    > connect-cluster-status    5            1
    
## Plugins

In order to use Kafka Connect JDBC, we create a custom container image that extends the normal base image with the JDBC connector plugin:

    buildah bud -t localhost:32000/kafka-with-plugins:0.20.0-kafka-2.6.0-1

We then push this custom image to the container registry so we can use it:

    buildah push --tls-verify=false localhost:32000/kafka-with-plugins:0.20.0-kafka-2.6.0-1
