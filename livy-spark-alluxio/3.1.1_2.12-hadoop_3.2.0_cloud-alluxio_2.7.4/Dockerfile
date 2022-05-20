ARG SPARK_BASE=registry.cn-hangzhou.aliyuncs.com/imodel/livy-spark:0.8.0-incubating-spark_3.1.1_2.12-hadoop_3.2.0_cloud_TT7
ARG ALLUXIO=alluxio/alluxio:2.7.4

FROM $ALLUXIO as build

FROM $SPARK_BASE

COPY --from=build /opt/alluxio/client/alluxio-2.7.4-client.jar /opt/spark/jars/alluxio-2.7.4-client.jar