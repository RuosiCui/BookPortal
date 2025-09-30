# FROM maven:3.8-openjdk-18 AS build
# COPY src /usr/src/app/src
# COPY pom.xml /usr/src/app
# RUN mvn -f /usr/src/app/pom.xml clean package

# FROM openjdk:18-alpine
# COPY --from=build /usr/src/app/target/BookPortal-0.0.1-SNAPSHOT.jar /usr/app/BookPortal-0.0.1-SNAPSHOT.jar
# EXPOSE 8080
# ENTRYPOINT ["java","-jar","/usr/app/BookPortal-0.0.1-SNAPSHOT.jar"]

# ---- Build stage ----
FROM maven:3.8-openjdk-18 AS build
WORKDIR /usr/src/app
# Copy POM first to leverage Docker layer cache
COPY pom.xml .
# Pre-fetch deps (optional but speeds up rebuilds)
RUN mvn -B -DskipTests dependency:go-offline
# Now copy sources
COPY src ./src
# Build without tests
RUN mvn -B -DskipTests clean package

# ---- Runtime stage ----
FROM openjdk:18-alpine
WORKDIR /usr/app
# Copy the built jar (wildcard so you don't depend on exact name)
COPY --from=build /usr/src/app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/usr/app/app.jar"]
