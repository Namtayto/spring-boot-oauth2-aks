FROM maven:3.8.5-openjdk-17 AS build
COPY . .
RUN mvn clean package -Pprod -DskipTests

FROM openjdk:17.0.1-jdk-slim
EXPOSE 8080
COPY applicationinsights-agent-3.4.19.jar /
COPY applicationinsights.json /
COPY --from=build /target/spring-social-0.0.1-SNAPSHOT.jar spring-social.jar
ENTRYPOINT ["java", "-javaagent:/applicationinsights-agent-3.4.19.jar","-jar","spring-social.jar"]