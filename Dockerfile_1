FROM ubuntu:latest

#this is a comment
#commonly only the maintainer label is used
LABEL maintainer="FleetyCodes - Laci"
LABEL creationdate="2026-08-16"

RUN apt-get update && apt-get install -y openjdk-8-jdk

WORKDIR /usr/local/bin/

#the java jar to run
COPY test-program.jar /usr/local/bin/
#COPY többet használtabb mint ADD (hasonló működés)

ENTRYPOINT ["java", "-jar", "test-program.jar"]
#ENTRYPOINT/CMD