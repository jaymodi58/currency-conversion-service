FROM openjdk:8-jre-alpine
 
MAINTAINER Jay Modi <jay.modi58@gmail.com>

VOLUME /tmp

ARG JAR_FILE

COPY ${JAR_FILE} app.jar

ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/app.jar"]