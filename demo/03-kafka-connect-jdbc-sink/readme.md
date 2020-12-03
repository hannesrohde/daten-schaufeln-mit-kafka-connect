# Scenario 3 - writing data from a kafka topic to a database

In this scenario, a JDBC sink connector is used to write the contents from a kafka topic to a SQL database.

We reuse the database from scenario 1, please follow the steps there to set up.

## Create a database table to hold the data

Connect to the database with tool of your choice (e.g. DBeaver) and add a table for persisted log messages:

    kubectl port-forward service/inventory-mysql 3306
    
    CREATE TABLE logs(
        id        INT NOT NULL AUTO_INCREMENT,
        source    VARCHAR NOT NULL,
        message   VARCHAR NOT NULL,
        timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        PRIMARY KEY (id)
    );

## Prepare topic and source connector

In this scenario we use a new topic and a source connector based on scenario 2:

    kubectl apply -f 01-kafka-topic.yaml
    kubectl apply -f 02-kafka-source-connector.yaml

The source connector transfers the same data as before, but now includes a JSON schema with the message.

## Deploy sink connector

Deploy the JDBC sink connector that writes the data into the database:

    kubectl apply -f 03-kafka-sink-connector.yaml

## Observe table contents

Shortly after the connector has been deployed, data should appear in the database table.
This can be observed with the database tool (see above).

## Append data to logfile

Run a shell in the kafka connect pod and add more lines to the file `messages.log`:

    kubectl exec -it my-connect-cluster-connect-84bc6bfcf5-c8lv5 /bin/bash
    $ echo "Hello again, world!" >> /tmp/messages.log
    $ echo "This is tech friday 2020" >> /tmp/messages.log

Shortly after adding the lines, new rows should appear in the database.
