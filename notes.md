# Daten schaufeln mit Kafka Connect

## Typischer erster Kontakt mit Kafka

Java / Spring Boot

Consumer überträgt Daten aus Kafka-Topic in die Datenbank

    class MyConsumer {
        @Autowired
        private Database db;
    
        @KafkaListener(
            topics = "my-topic", id = "my-consumer"
        )
        public void onMessage(
            @Header(RECEIVED_MESSAGE_KEY) String key,
            @Payload String message,
            Acknowledgement ack
        ) {
            if (message != null) {
                // persist item in database
                db.upsert(key, message);
            } else /* tombstone message */ {
                db.delete(key);
            }
            ack.acknowledeg();
        }
    }

Dazu kommt
- JPA Entity und Repository
- ggfs. Mapping / Transformation
- Error handling

 -> oft unnötige Fleißarbeit / Boilerplate

## Vorstellung Kafka Connect

eingeführt mit Kafka 0.9.0 (2015)

Vorgefertigte Connectoren zum Streamen von Daten zu und aus Kafka, die viele Standard-Usecases abdecken

rein konfigurativ, Basis-Funktionalität bereits implementiert

## Kafka Connect: Source und Sink

Source: Übertragung aus einer Datenquelle nach Kafka
    - Producer
    
Sink: Übertragung aus Kafka in eine Datensenke
    - Consumer

## Was kann man per Kafka Connect anbinden?

Connector-Plugins für viele Standard-Technologien

## 

Connect Cluster


- Workers


Welche Connectoren gibt es?

## Connectoren

Bundled:

- Confluent Replicator
- FileStream

Als Plugin:

- JDK-Code = Jar Files

### Interne Datenhaltung

- Configuration, Offsets, Status
- in Kafka!
- darüber auch automatisch Cluster-Bildung

### Converter

https://docs.confluent.io/current/connect/userguide.html#configuring-key-and-value-converters

- Key und Value müssen konvertiert werden
-- AvroConverter
-- ProtobufConverter
-- JsonSchemaConverter
-- JsonConverter
-- StringConverter
-- ByteArrayConverter = Rohdaten

### Standalone oder distributed

- Connector Tasks laufen als "worker"
- Standalone: alles in einer Maschine (1 Pod)
-- speichert in lokalem Filesystem
- Distributed: Worker auf mehreren Maschinen verteilt laufen lassen = Connect Cluster
-- skalierbar
-- Fehlertolerant: Bei Ausfall eines Nodes werden Tasks neu verteilt. Datenhaltung in Kafka -> Stateless -> kein Datenverlust

### Configuration

per REST-API
oder strimzi Operator auf Kubernetes

## Einsatzbeispiele

### Szenario 1:

Daten aus einer Legacy-Applikation sollen per Kafka verteilt werden

- Kunde hat Infor ERP auf IBM AS400
- Datenbank IBM DB2
- benötigt: Stamm- und Bewegtdaten
- Daten müssen konvertiert/aufgearbeitet werden 
- Keine Möglichkeit, eigenen Code auf Maschine zu deployen
- aber (wegen architektonischer Altlasten): Datenbank zugänglich 

-> Kafka Connect JDBC, JDBC-Treiber für DB2
-> Queries schreiben 

### Szenario 2 (Steuerung)

- Barcode-Scanner am Fließband, gescannte Barcodes werden in Logfile geschrieben
- Heizungsthermostat
- ...
- generiert periodisch Log-Zeilen

-> Kafka Connect Filestream 

## Sonstiges

### Verwendung mit Schema Registry
    - Avro / Protobuf / JSON Schema
    - Schema Evolution
    - Forcierte Kompatibilität

# Live Demo

Buildah / Podman / Skopeo = Ersatz für Docker mit Daemon




# Vids
https://www.youtube.com/watch?v=Jkcp28ki82k

# Doku
https://docs.confluent.io/current/connect/userguide.html
https://docs.confluent.io/current/connect/index.html

# Pics
https://unsplash.com/photos/9jPJrfLTBi0
https://unsplash.com/photos/ZgmGq_eFmUs
https://unsplash.com/s/photos/excavator


