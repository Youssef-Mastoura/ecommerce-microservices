# notification-service/Dockerfile
FROM maven:3.8.6-openjdk-17-slim AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline -B
COPY src ./src
RUN mvn clean package -DskipTests

FROM openjdk:17-jre-slim
WORKDIR /app
RUN groupadd -r spring && useradd -r -g spring spring
USER spring

# Health check (adjust path if different)
HEALTHCHECK --interval=30s --timeout=3s --start-period=120s --retries=3 \
  CMD curl -f http://localhost:8087/actuator/health || exit 1

COPY --from=builder /app/target/*.jar app.jar

# Notification service typically runs on port 8087
EXPOSE 8087

# If your app uses a different port, adjust here:
ENTRYPOINT ["java", "-jar", "-Dserver.port=8087", "app.jar"]