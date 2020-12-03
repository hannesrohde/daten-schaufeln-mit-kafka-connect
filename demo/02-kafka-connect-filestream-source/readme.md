# Scenario 2 - reading data from logfiles

In this scenario, a Filestream source connector is used to transmit data from a logfile to Kafka.

## Create Kafka topic

Create new, empty topic 'logfiles':

    kubectl apply -f 01-kafka-topic.yaml
    kubectl get kafkatopics

Observe the Kafka topic with `kaf`:

    kaf consume logfiles

## Create logfile

Run a shell in the kakfa connect pod and create the file `messages.log`:

    kubectl exec -it my-connect-cluster-connect-84bc6bfcf5-c8lv5 /bin/bash
    $ echo "Hello world!" >> /tmp/messages.log

## Deploy source connector

Deploy the source connector:

    kubectl apply -f 02-kafka-connector.yaml

Shortly after deployment, the connector starts exporting data.
This can be observed with `kaf` (see above).

## Append data to logfile

Run a shell in the kakfa connect pod and add more lines to the file `messages.log`:

    kubectl exec -it my-connect-cluster-connect-84bc6bfcf5-c8lv5 /bin/bash
    $ echo "Hello again, world!" >> /tmp/messages.log
    $ echo "This is tech friday 2020" >> /tmp/messages.log

The new data can be observed with `kaf` (see above).

## Cleanup

Delete all resources we created for scenario 2:

    kubectl delete all,kafkatopic,kafkaconnector -l scenario=2
