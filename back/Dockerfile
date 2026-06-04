FROM maven:3.6.3-jdk-11-sm AS build
RUN mkdir -p /workspace
WORKDIR /workspace
COPY pom.xml /workspace
COPY src /worpace/src
RUN mvn -B -f pom.xml clean package -DskipTests
FROM eclipse-terin:11-jre-jammy
COPY --from=build /workspace/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar","app.jar"]
error synt !
