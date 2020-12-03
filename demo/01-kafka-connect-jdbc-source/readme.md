# Scenario 1 - exporting data from MySQL

In this scenario, a JDBC source connector is used to export the results of an SQL query to Kafka. 

## Create Kafka topic

Create new, empty topic 'inventory-orders':

    kubectl apply -f 01-kafka-topic.yaml
    kubectl get kafkatopics
    
Observe the Kafka topic with `kaf`:

    kaf consume inventory-orders

## Deploy MySQL database

Run a MySQL instance that is pre-populated with a simple inventory/orders database:

    kubectl apply -f 02-inventory-mysql.yaml

Connect to the database with tool of your choice (e.g. DBeaver) and look at the contents

    kubectl port-forward service/inventory-mysql 3306
    $ SELECT * FROM orders;

## Deploy source connector

Deploy the source connector:

    kubectl apply -f 03-kafka-connector.yaml

Shortly after deployment, the connector starts exporting data.
This can be observed with `kaf` (see above).

## Insert new order

Connect to the database with tool of your choice (e.g. DBeaver)

    kubectl port-forward service/inventory-mysql 3306
    $ INSERT INTO orders(order_number, order_date, purchaser, quantity, product_id) VALUES (12345, '2020-12-04', 1004, 1, 108);

Shortly after insertion, the new order appears on the topic.
This can be observed with `kaf` (see above).
