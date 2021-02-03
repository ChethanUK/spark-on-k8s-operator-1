#
# Copyright 2018 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

ARG SPARK_IMAGE=chethanuk/spark:v3.1.1

FROM ${SPARK_IMAGE}

# Versions
ARG HADOOP_VERSION=3
ARG JMX_VERSION=0.15.0
ARG GCS_CONNECTOR_VERSION=hadoop3-2.2.0
ARG GOOGLE_GUAVA=30.0-jre
# Names and URI's
ARG GCS_CONNECTOR_NAME=gcs-connector-${GCS_CONNECTOR_VERSION}.jar
ARG GCS_CONNECTOR_URI=https://storage.googleapis.com/hadoop-lib/gcs/${GCS_CONNECTOR_NAME}
ARG JMX_JAVAAGENT_JAR_NAME=jmx_prometheus_javaagent-${JMX_VERSION}.jar
ARG JMX_JAVAAGENT_JAR_URI=https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/${JMX_VERSION}/${JMX_JAVAAGENT_JAR_NAME}
ARG SPARK_BIGQUERY_JAR_NAME=spark-bigquery-latest.jar
ARG SPARK_BIGQUERY_JAR_URI=https://storage.googleapis.com/spark-lib/bigquery/${SPARK_BIGQUERY_JAR_NAME}
ARG GOOGLE_GUAVA_JAR_NAME=guava-${GOOGLE_GUAVA}.jar
ARG GOOGLE_GUAVA_JAR_URI=https://repo1.maven.org/maven2/com/google/guava/guava/${GOOGLE_GUAVA}/${GOOGLE_GUAVA_JAR_NAME}

# Switch to user root so we can add addtional jars and configuration files.
USER root

# Setup dependencies for Google Cloud Storage access.
# RUN ls $SPARK_HOME/jars/ && \
RUN rm $SPARK_HOME/jars/guava-14.0.1.jar && \
  echo "Downloading Google guava Jar from ${GOOGLE_GUAVA_JAR_URI}" && \
  wget -q -O $SPARK_HOME/jars/${GOOGLE_GUAVA_JAR_NAME} ${GOOGLE_GUAVA_JAR_URI} && \
  chmod 644 $SPARK_HOME/jars/${GOOGLE_GUAVA_JAR_NAME}  &&\
  # Setup for the Prometheus JMX exporter.
  # Add the Prometheus JMX exporter Java agent jar for exposing metrics sent to the JmxSink to Prometheus.
  echo "Downloading JMX exporter from ${JMX_JAVAAGENT_JAR_URI}" && \
  mkdir -p /prometheus/ && \
  wget -q -O /prometheus/${JMX_JAVAAGENT_JAR_NAME} ${JMX_JAVAAGENT_JAR_URI} && \
  chmod 644 /prometheus/${JMX_JAVAAGENT_JAR_NAME}

# Add the connector jar needed to access Google Cloud Storage using the Hadoop FileSystem API.
RUN echo "Downloading GCS Hadoop connector jar from ${GCS_CONNECTOR_URI}" && \
  wget -q -O $SPARK_HOME/jars/${GCS_CONNECTOR_NAME} ${GCS_CONNECTOR_URI} && \
  chmod 644 $SPARK_HOME/jars/${GCS_CONNECTOR_NAME} && \
  echo "Downloading Spark BigQuery jar from ${SPARK_BIGQUERY_JAR_URI}" && \
  wget -q -O $SPARK_HOME/jars/${SPARK_BIGQUERY_JAR_NAME} ${SPARK_BIGQUERY_JAR_URI} && \
  chmod 644 $SPARK_HOME/jars/${SPARK_BIGQUERY_JAR_NAME}

USER ${spark_uid}

RUN mkdir -p /etc/metrics/conf
COPY conf/metrics.properties /etc/metrics/conf
COPY conf/prometheus.yaml /etc/metrics/conf

ENTRYPOINT ["/opt/entrypoint.sh"]
