# E-Commerce Config Server

`ecommerce-config-server` is the centralized configuration management service for the E-Commerce Microservices application.

## What Config Server Is

Spring Cloud Config Server exposes configuration files over HTTP so applications can load their settings from one managed place instead of keeping environment-specific values inside every service.

## Why Centralized Configuration Is Useful

Centralized configuration keeps service settings consistent, versioned, and easier to change. Database URLs, service ports, Eureka settings, feature flags, and future Kafka settings can be updated in the configuration repository and then fetched by the matching service.

## Microservices

- `discovery-service`: Runs Eureka Server so services can register themselves and discover each other.
- `api-gateway`: Routes external traffic to backend services and can use service discovery for routing.
- `user-service`: Manages user data and uses the `user_service` database schema.
- `product-service`: Manages product catalog data and uses the `product_service` database schema.
- `order-service`: Manages orders and uses the `order_service` database schema.
- `notification-service`: Handles notifications. Kafka integration will be added later.

## Git Configuration Repository

The Config Server reads configuration from a Git repository configured in `src/main/resources/application.properties`.

```properties
spring.cloud.config.server.git.uri=https://github.com/<github-username>/ecommerce-config-repo
```

Replace `<github-username>` with your GitHub username or organization. Create a separate GitHub repository named `ecommerce-config-repo` and add the sample property files from the local `ecommerce-config-repo` directory in this project.

Expected configuration repository structure:

```text
ecommerce-config-repo/
  discovery-service.properties
  api-gateway.properties
  user-service.properties
  product-service.properties
  order-service.properties
  notification-service.properties
```

## How Services Fetch Configuration

Each microservice uses its `spring.application.name` to request the matching property file from the Config Server. For example, `user-service` requests `user-service.properties`.

The Config Server runs on:

```properties
server.port=8888
```

Each microservice should include the Spring Cloud Config Client dependency and configure `spring.config.import` in its own local `application.properties`.

## Microservice Config Client Settings

`discovery-service`:

```properties
spring.application.name=discovery-service
spring.config.import=configserver:http://localhost:8888/
```

`api-gateway`:

```properties
spring.application.name=api-gateway
spring.config.import=configserver:http://localhost:8888/
```

`user-service`:

```properties
spring.application.name=user-service
spring.config.import=configserver:http://localhost:8888/
```

`product-service`:

```properties
spring.application.name=product-service
spring.config.import=configserver:http://localhost:8888/
```

`order-service`:

```properties
spring.application.name=order-service
spring.config.import=configserver:http://localhost:8888/
```

`notification-service`:

```properties
spring.application.name=notification-service
spring.config.import=configserver:http://localhost:8888/
```

## Local Startup Sequence

1. Config Server
2. Discovery Service
3. API Gateway
4. User Service
5. Product Service
6. Order Service
7. Notification Service
8. Kafka later

## Current Scope

This project only configures Spring Cloud Config Server and sample service properties. Docker, Kubernetes, security, JWT, OAuth, Kafka implementation, unit tests, and integration tests are intentionally not included.
