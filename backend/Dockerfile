# ---- Stage 1: Build the application ----
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn clean package -DskipTests

# ---- Stage 2: Production Image ----
FROM eclipse-temurin:21-jre-jammy
WORKDIR /app

# Only copy the fat JAR from the builder image
COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
