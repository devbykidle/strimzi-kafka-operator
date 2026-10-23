FROM strimzi/base:latest

LABEL org.opencontainers.image.source='https://github.com/strimzi/strimzi-kafka-operator'

ARG KAFKA_DIST_DIR
ARG KAFKA_VERSION
ARG THIRD_PARTY_LIBS
ARG strimzi_version
ARG TARGETOS
ARG TARGETARCH

LABEL name='kafka' \
    version="${strimzi_version}-${KAFKA_VERSION}" \
    release="${strimzi_version}" \
    summary="Kafka ${KAFKA_VERSION} image of the Strimzi Kafka Operator project." \
    description="Kafka image used by the Strimzi Kafka Operator to run Kafka ${KAFKA_VERSION} clusters."

# Add kafka user with UID 1001
# The user is in the group 0 to have access to the mounted volumes and storage
RUN useradd -r -m -u 1001 -g 0 kafka

#####
# Add Kafka
#####
ENV KAFKA_HOME=/opt/kafka
ENV KAFKA_VERSION=${KAFKA_VERSION}
ENV STRIMZI_VERSION=${strimzi_version}

COPY $KAFKA_DIST_DIR $KAFKA_HOME
COPY ./scripts/ $KAFKA_HOME
RUN mkdir $KAFKA_HOME/plugins

#####
# Add Kafka Exporter
#####
ENV KAFKA_EXPORTER_HOME=/opt/kafka-exporter
ENV KAFKA_EXPORTER_VERSION=1.11.0
ENV KAFKA_EXPORTER_CHECKSUM_AMD64="8836e92827132dd232010a1c53fc7e3a815b4c9e47b8b5791957c43ad1a839035482ea69aca42891e626b6a96bcd791cc863cc64315ce519d0ea4604090a3a93  kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-amd64.tar.gz"
ENV KAFKA_EXPORTER_CHECKSUM_ARM64="8c5d3eadf086dea24c49962ef058a61b8457ec52b6173834b3b311066914874235de439aa2e97ad6e59bc95bba577bc3ebd663edc03afaa4440551b451d69f63  kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-arm64.tar.gz"
ENV KAFKA_EXPORTER_CHECKSUM_PPC64LE="6a25e0b953dc3663b3439bf1f17bc525e7735d2de6cddda36e90f4cf3db8ea541f5e1b6647db678a61014e4ce216aa96ebd909c277a759c0cb1e4de31a565599  kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-ppc64le.tar.gz"
ENV KAFKA_EXPORTER_CHECKSUM_S390X="876423ca7513ada825a17e657662bee1644b47471e61cc13758ed6296c32b34492b301c41e287a40ce674dd5cd71f43dc47bdac674484d87db14296221c4bb0d  kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-s390x.tar.gz"

RUN set -ex; \
    if [[ "${TARGETOS}/${TARGETARCH}" = "linux/arm64" ]]; then \
        curl -LO https://github.com/danielqsj/kafka_exporter/releases/download/v${KAFKA_EXPORTER_VERSION}/kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-arm64.tar.gz; \
        echo $KAFKA_EXPORTER_CHECKSUM_ARM64 > kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-arm64.tar.gz.sha512; \
        sha512sum --check kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-arm64.tar.gz.sha512; \
        mkdir $KAFKA_EXPORTER_HOME; \
        tar xvfz kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-arm64.tar.gz -C $KAFKA_EXPORTER_HOME --strip-components=1; \
        rm -f kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-arm64.tar.gz*; \
    elif [[ "${TARGETOS}/${TARGETARCH}" = "linux/ppc64le" ]]; then \
        curl -LO https://github.com/danielqsj/kafka_exporter/releases/download/v${KAFKA_EXPORTER_VERSION}/kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-ppc64le.tar.gz; \
        echo $KAFKA_EXPORTER_CHECKSUM_PPC64LE > kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-ppc64le.tar.gz.sha512; \
        sha512sum --check kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-ppc64le.tar.gz.sha512; \
        mkdir $KAFKA_EXPORTER_HOME; \
        tar xvfz kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-ppc64le.tar.gz -C $KAFKA_EXPORTER_HOME --strip-components=1; \
        rm -f kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-ppc64le.tar.gz*; \
    elif [[ "${TARGETOS}/${TARGETARCH}" = "linux/s390x" ]]; then \
        curl -LO https://github.com/danielqsj/kafka_exporter/releases/download/v${KAFKA_EXPORTER_VERSION}/kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-s390x.tar.gz; \
        echo $KAFKA_EXPORTER_CHECKSUM_S390X > kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-s390x.tar.gz.sha512; \
        sha512sum --check kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-s390x.tar.gz.sha512; \
        mkdir $KAFKA_EXPORTER_HOME; \
        tar xvfz kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-s390x.tar.gz -C $KAFKA_EXPORTER_HOME --strip-components=1; \
        rm -f kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-s390x.tar.gz*; \
    else \
        curl -LO https://github.com/danielqsj/kafka_exporter/releases/download/v${KAFKA_EXPORTER_VERSION}/kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-amd64.tar.gz; \
        echo $KAFKA_EXPORTER_CHECKSUM_AMD64 > kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-amd64.tar.gz.sha512; \
        sha512sum --check kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-amd64.tar.gz.sha512; \
        mkdir $KAFKA_EXPORTER_HOME; \
        tar xvfz kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-amd64.tar.gz -C $KAFKA_EXPORTER_HOME --strip-components=1; \
        rm -f kafka_exporter-${KAFKA_EXPORTER_VERSION}.linux-amd64.tar.gz*; \
    fi
COPY ./exporter-scripts $KAFKA_EXPORTER_HOME
RUN chmod 0755 \
      /opt/kafka-exporter/kafka_exporter_run.sh \
      /opt/kafka-exporter/kafka_exporter

#####
# Add Prometheus JMX Exporter
#####
ENV JMX_EXPORTER_HOME=/opt/prometheus-jmx-exporter
ENV JMX_EXPORTER_VERSION=1.5.0
ENV JMX_EXPORTER_CHECKSUM="13f22f11b383acfcada201244c4b33b0219048cb9a190b47e132c6e0f3c60a15"

RUN set -ex; \
    curl -LO https://github.com/prometheus/jmx_exporter/releases/download/${JMX_EXPORTER_VERSION}/jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar; \
    echo $JMX_EXPORTER_CHECKSUM > jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar.sha256; \
    #sha256sum -c jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar.sha256; \
    mkdir $JMX_EXPORTER_HOME; \
    mv jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar $JMX_EXPORTER_HOME/;
    #rm -f jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar.sha256;

#####
# Add Strimzi agents
#####
COPY ./tmp/kafka-agent-${STRIMZI_VERSION}.jar ${KAFKA_HOME}/libs/
COPY ./tmp/tracing-agent-${STRIMZI_VERSION}.jar ${KAFKA_HOME}/libs/

#####
# Add 3rd party libs
#####
COPY tmp/thirdparty-libs-${THIRD_PARTY_LIBS}/ ${KAFKA_HOME}/libs/

#####
# Add Cruise Control
#####
ENV CRUISE_CONTROL_HOME=/opt/cruise-control
RUN mkdir $CRUISE_CONTROL_HOME
COPY tmp/cc/ ${CRUISE_CONTROL_HOME}/libs/
COPY ./cruise-control-scripts $CRUISE_CONTROL_HOME

WORKDIR $KAFKA_HOME

USER 1001
