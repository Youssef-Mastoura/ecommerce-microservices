# product-service/Dockerfile
FROM registry.access.redhat.com/ubi8/openjdk-17:1.18 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests

FROM openjdk:17-jre-slim
WORKDIR /app

# Install curl for health checks
RUN apt-get update && apt-get install -y curl && rm -rf /var/lib/apt/lists/*

RUN groupadd -r spring && useradd -r -g spring spring
USER spring

HEALTHCHECK --interval=30s --timeout=3s --start-period=120s --retries=3 \
  CMD curl -f http://localhost:8081/actuator/health/liveness || exit 1

COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8081  # Good
ENTRYPOINT ["java", "-jar", "-Dserver.port=8081", "app.jar"]
