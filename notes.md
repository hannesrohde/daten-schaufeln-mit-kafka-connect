
## Einsatzbeispiele

### Szenario 1:

Daten aus einer Legacy-Applikation sollen per Kafka verteilt werden

- Kunde hat Infor ERP auf IBM AS400
- Datenbank IBM DB2
- benötigt: Stamm- und Bewegtdaten
- Daten müssen konvertiert/aufgearbeitet werden 
- Keine Möglichkeit, eigenen Code auf Maschine zu deployen
- aber (wegen architektonischer Altlasten): Datenbank zugänglich 

https://de.confluent.io/blog/kafka-connect-deep-dive-jdbc-source-connector/




Daten:
https://www.mysqltutorial.org/mysql-sample-database.aspx/



Einschränkung:

- geänderte Daten erkennen: Timestamp, lfd. Id
- gelöschte Daten nicht erkennbar

-> Debezium CDC

Live Demo:

! Offset zurücksetzen !

- Schauen
  kubectl get all -l scenario=1
- Datenbank deployen
  kubectl apply -f 01-inventory-mysql.yaml
- in Datenbank schauen
  (eigenes Tab) kubectl port-forward service/inventory-mysql 3306
  DBeaver starten
- Topic anlegen
  kaf topics
  kubectl apply -f 02-kafka-topic.yaml
  kaf topics
- Topic überwachen
  (eigenes Tab) kaf consume inventory-orders
- Connector deployen
  kubectl apply -f 03-kafka-connector.yaml
- Neuen Datensatz einfügen
  DBeaver 
  INSERT INTO orders(order_number, order_date, purchaser, quantity, product_id)
  VALUES (12345, '2020-12-04', 1004, 1, 108);
- (Neue Message taucht auf in kaf)
- Cleanup
  kubectl delete all,kafkatopic,kafkaconnector -l scenario=1

### Szenario 2 (Steuerung)

- Barcode-Scanner am Fließband, gescannte Barcodes werden in Logfile geschrieben
- Heizungsthermostat
- ...
- generiert periodisch Log-Zeilen

-> Kafka Connect Filestream

CSV: https://rmoff.net/2020/06/17/loading-csv-data-into-kafka/

### Szenario 3 (Logging)
 
 - nginx access log 

### Szenario 4 (Testdaten)

- datagen source connector

https://de.confluent.io/blog/easy-ways-generate-test-data-kafka/

## Sonstiges

### Verwendung mit Schema Registry
    - Avro / Protobuf / JSON Schema
    - Schema Evolution
    - Forcierte Kompatibilität
    
https://docs.confluent.io/platform/current/schema-registry/index.html

# Live Demo

Buildah / Podman / Skopeo = Ersatz für Docker mit Daemon

# Nachteile

- Debugging von Fehlern manchmal schwer

# Vids
https://www.youtube.com/watch?v=Jkcp28ki82k

# Doku
https://docs.confluent.io/current/connect/userguide.html
https://docs.confluent.io/current/connect/index.html

# Pics
https://unsplash.com/photos/9jPJrfLTBi0
https://unsplash.com/photos/ZgmGq_eFmUs
https://unsplash.com/s/photos/excavator


# Offene Fragen:

- Guarantees:  at least once, at most once, and will support exactly once in a future release when that capability is available natively within Kafka
- DLQ https://docs.confluent.io/current/connect/concepts.html#dead-letter-queue
- Licensing

# TODO

- snapd, microk8s lokal intallieren

    sudo pacman -S snapd
    sudo systemctl enable --now snapd.socket
    # support for classic snaps
    ln -s /var/lib/snapd/snap /snap



    # reset consumer offset for a connector
    kafkacat -b my-cluster-kafka-bootstrap.kafka-demo.svc.cluster.local:9092 -C -t connect-cluster-offsets -K# -f 'Partition %p Key %k Value %s\n' -e -Z -q
    echo '["jdbc-source-inventory-orders",{"query":"query"}]#' | kafkacat -b my-cluster-kafka-bootstrap.kafka-demo.svc.cluster.local:9092 -P -t connect-cluster-offsets -K# -Z -p 4
