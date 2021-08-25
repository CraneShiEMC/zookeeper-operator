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
FROM  ${DOCKER_REGISTRY:+$DOCKER_REGISTRY/}openjdk:11-jdk
RUN mkdir /zu
COPY zu /zu
WORKDIR /zu
RUN ./gradlew --console=verbose --info shadowJar

FROM ${DOCKER_REGISTRY:+$DOCKER_REGISTRY/}zookeeper:3.6.1
COPY bin /usr/local/bin
RUN chmod +x /usr/local/bin/*
COPY log4j.properties /apache-zookeeper-3.6.1-bin/conf/log4j.properties
COPY zk-sp.sh /zk-sp.sh
RUN chmod +x /zk-sp.sh
COPY --from=0 /zu/build/libs/zu.jar /root/
RUN chmod +x -R /docker-entrypoint.sh
RUN chgrp -R 0 /apache-zookeeper-3.6.1-bin && \
    chmod -R g=u /apache-zookeeper-3.6.1-bin
RUN chgrp -R 0 /root && \
    chmod -R g=u /root
ENV ZOO_LOG_DIR=/opt/zookeeper/logs
ENV ZOO_LOG4J_PROP=DEBUG,ROLLINGFILE
RUN sed -i 's/ZOO_LOG_FILE=.*/ZOO_LOG_FILE=zookeeper.log/' /apache-zookeeper-3.6.1-bin/bin/zkServer.sh
RUN apt-get -q update && \
    apt-get install -y dnsutils curl procps
