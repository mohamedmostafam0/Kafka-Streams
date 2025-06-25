# Kafka Streams Demo

This project demonstrates a simple Kafka Streams application that performs a word count on text input using Apache Kafka's Streams API. It uses a Dockerized Kafka setup with KRaft mode (without ZooKeeper) for local development and testing.

## Prerequisites

- **Docker**: Ensure Docker and Docker Compose are installed.
- **Java**: Java 11 or later is required for the Kafka Streams application.
- **Maven**: Maven is used to build and run the Java application.
- **Kafka CLI**: The `kafka-cli` container provides tools like `kafka-topics.sh` and `kafka-console-producer.sh`.

## Project Structure

- **`docker-compose.yaml`**: Defines a Kafka service (using `apache/kafka-native`) and a CLI container for interacting with Kafka.
- **`kafka-streams-demo/`**: Contains the Maven project for the Kafka Streams application.
  - **`pom.xml`**: Maven configuration with Kafka Streams dependency and plugins.
  - **`src/main/java/.../WordCountDemo.java`**: The main Java application implementing the word count logic.
  - **`target/`**: Contains compiled class files and Maven build status.

## Setup Instructions

### Start Kafka Cluster
```bash
docker-compose up -d
```
This starts a single-node Kafka cluster in KRaft mode and a CLI container.

## Additional Steps

### Create input and output topics using the kafka-cli container:
```bash
docker exec -it kafka-cli kafka-topics.sh --bootstrap-server localhost:9092 --create --topic streams-plaintext-input --partitions 1 --replication-factor 1
docker exec -it kafka-cli kafka-topics.sh --bootstrap-server localhost:9092 --create --topic streams-wordcount-output --partitions 1 --replication-factor 1
```

### Build the Kafka Streams application:
```bash
cd kafka-streams-demo
mvn clean package
```
### Run the Kafka Streams application:
```bash
mvn exec:java
```

### Produce sample data:
```bash
docker exec -it kafka-cli kafka-console-producer.sh --bootstrap-server localhost:9092 --topic streams-plaintext-input
```
Type text (e.g., hello world) and press Enter. Use Ctrl+C to exit.

Consume output:
```bash
docker exec -it kafka-cli kafka-console-consumer.sh --bootstrap-server localhost:9092
```


### How It Works
The Kafka Streams application reads text from the streams-plaintext-input topic.
It splits each line into words, converts them to lowercase, and counts occurrences using the Kafka Streams API.
The results (word and count) are written to the streams-wordcount-output topic.
The application uses the high-level KStream DSL and connects to the Kafka broker at localhost:9092.


### Configuration Details
Kafka Setup: Uses apache/kafka-native with KRaft mode, configured for host (localhost:9092) and Docker (kafka:9093) communication.

Kafka Streams Application: Built with Java 11 and Kafka Streams 3.6.1, with application ID streams-wordcount.
