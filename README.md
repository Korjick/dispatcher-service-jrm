# Dispatcher Service

An event-driven microservice for processing customer orders and publishing dispatch results through Kafka.

Part of: https://github.com/topics/korjick-java-microservice-restaurant

## Tech Stack

- **Language & Framework:** Java 25, Spring Boot 4 (Spring Web, Spring Kafka)
- **Messaging:** Apache Kafka (Manual Acknowledgment, Idempotent Producer)
- **Serialization:** Apache Avro, Confluent Schema Registry
- **Configuration:** Spring Cloud Config (with Retry & Backoff)
- **Monitoring:** Spring Boot Actuator
- **Utilities:** Lombok
- **Testing:** JUnit 5, Testcontainers (Kafka, Schema Registry), Spring Kafka Test, AssertJ
- **Containerization:** Cloud Native Buildpacks

## Kafka Events

- `v1.public.orders_outbox` - Consume `AvroOrderPlacedEvent` messages containing order and delivery details.
- `v1.orders_dispatch` - Publish `OrderDispatchedEvent` messages containing the order ID and dispatch status (`ACCEPTED` or `REJECTED`), preserving the original message key.

The current processing logic accepts orders with even IDs and rejects orders with odd IDs. Consumer offsets are acknowledged after the dispatch event is successfully published; failed sends trigger a negative acknowledgment for redelivery.

## Build and Run

Requires Java 25. Docker must be running for integration tests and container image builds. To run the application, provide Kafka at `localhost:9097` and Schema Registry at `http://localhost:8081`.

The application listens on port `9096`. The optional Config Server defaults to `http://localhost:9095` and can be configured with `CONFIG_SERVER_URL`.

```bash
# Run tests
./gradlew test

# Run application
./gradlew bootRun

# Build container image
./gradlew bootBuildImage
```

On Windows, use `.\gradlew.bat` instead of `./gradlew`.
