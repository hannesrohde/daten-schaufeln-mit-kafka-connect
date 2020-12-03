# Scenario 2 - reading data from logfiles

In this scenario, a Filestream source connector is used to transmit data from a logfile to Kafka.

## Create Kafka topic

Create new, empty topic 'logfiles':

    kubectl apply -f 01-kafka-topic.yaml
    kubectl get kafkatopics

Observe the Kafka topic with `kaf`:

    kaf consume logfiles

## Deploy source connector

Deploy the source connector:

    kubectl apply -f 02-kafka-connector.yaml

Shortly after deployment, kafka connect logs warnings complaining that the log file is not there:

    kubectl logs -f my-connect-cluster-connect-55846645d9-6znnq

## Create logfile

Run a shell in the kakfa connect pod and create the file `messages.log`:

    kubectl exec -it my-connect-cluster-connect-84bc6bfcf5-c8lv5 /bin/bash
    $ echo 'Hello world!' >> /tmp/messages.log

Append more data to logfile:

    $ echo 'Hello again, world!' >> /tmp/messages.log
    $ echo 'This is tech friday 2020' >> /tmp/messages.log

Each new line can be observed with `kaf` (see above).
