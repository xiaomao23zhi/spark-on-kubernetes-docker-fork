ARG LIVY_BUILDER_TAG=latest
ARG SPARK_TAG=latest

### Livy Builder Container
FROM 878256633362.dkr.ecr.us-west-2.amazonaws.com/livy-builder:${LIVY_BUILDER_TAG} as build

ARG LIVY_GITHUB_REPO=tatari-tv/incubator-livy
ARG LIVY_GITHUB_BRANCH=master

RUN git clone https://github.com/${LIVY_GITHUB_REPO}.git --branch ${LIVY_GITHUB_BRANCH} --single-branch --depth=50 && \
    cd incubator-livy && \
    mvn clean package -B -V -e \
        -Pspark-3.1 \
        -Pthriftserver \
        -DskipTests \
        -DskipITs \
        -Dmaven.javadoc.skip=true && \
    cp assembly/target/apache-livy-0.8.0-incubating-SNAPSHOT-bin.zip /

### Final Container
FROM 878256633362.dkr.ecr.us-west-2.amazonaws.com/spark:${SPARK_TAG}

ARG LIVY_VERSION_ARG=0.8.0-incubating-SNAPSHOT

LABEL maintainer="Jerome Banks <jerome@tatari.tv>"

ENV LIVY_VERSION            $LIVY_VERSION_ARG
ENV LIVY_HOME               /opt/livy
ENV LIVY_CONF_DIR           $LIVY_HOME/conf

ENV PATH                    $PATH:$LIVY_HOME/bin

# install livy
COPY --from=build /apache-livy-${LIVY_VERSION}-bin.zip /
USER root
RUN apt-get install -y unzip && \
    unzip /apache-livy-${LIVY_VERSION}-bin.zip -d / && \
    mv /apache-livy-${LIVY_VERSION}-bin /opt/ && \
    rm -rf $LIVY_HOME && \
    ln -s /opt/apache-livy-${LIVY_VERSION}-bin $LIVY_HOME && \
    rm -f /apache-livy-${LIVY_VERSION}-bin.zip
USER ${spark_uid}

COPY Dockerfile /my_docker/

#  4040 - Spark UI port
#  7078 - Driver RPC port
#  7079 - Blockmanager port
#  8088 - JMX Exporter for Prometheus
# 10000 - Livy RPC Server for Jupyter integration
# 10015 - Spark Thrift server port
EXPOSE 4040 7078 7079 8088 10000 10015
