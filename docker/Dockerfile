#
# Copyright (c) 2018 Dell Inc., or its subsidiaries. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#

ARG DOCKER_REGISTRY
ARG ZK_VERSION=3.6.3
FROM  ${DOCKER_REGISTRY:+$DOCKER_REGISTRY/}openjdk:11-jdk
RUN mkdir /zu
COPY zu /zu
WORKDIR /zu
RUN ./gradlew --console=verbose --info shadowJar

FROM ${DOCKER_REGISTRY:+$DOCKER_REGISTRY/}zookeeper:${ZK_VERSION}
ENV ZK_USER=container
# Add container user
RUN useradd -g 1000 ${ZK_USER}
COPY bin /usr/local/bin
COPY --from=0 /zu/build/libs/zu.jar /opt/libs/
COPY log4j.properties /apache-zookeeper-${ZK_VERSION}-bin/conf/log4j.properties
COPY zk-sp.sh /zk-sp.sh

RUN chown -R ${ZK_USER}:1000 /usr/local/bin/ && \
    chmod +x -R /usr/local/bin/

RUN chown ${ZK_USER}:1000 /zk-sp.sh && \
    chmod +x /zk-sp.sh

RUN chown ${ZK_USER}:1000 /docker-entrypoint.sh && \
    chmod +x /docker-entrypoint.sh

RUN chown -R ${ZK_USER}:1000 /apache-zookeeper-${ZK_VERSION}-bin/ && \
    chmod +x -R /apache-zookeeper-${ZK_VERSION}-bin/

ENV ZOO_LOG_DIR=/opt/zookeeper/logs
ENV ZOO_LOG4J_PROP=INFO,ROLLINGFILE
ARG ZK_VERSION=3.6.3
RUN sed -i 's/ZOO_LOG_FILE=.*/ZOO_LOG_FILE=zookeeper.log/' /apache-zookeeper-${ZK_VERSION}-bin/bin/zkServer.sh
RUN apt-get -q update && \
    apt-get install -y vim dnsutils curl procps
USER ${ZK_USER}
