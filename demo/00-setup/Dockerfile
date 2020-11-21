FROM docker.io/strimzi/kafka:0.20.0-kafka-2.6.0

USER root:root

# Add our plugins to base image
COPY ./plugins /opt/kafka/plugins/

USER 1001
