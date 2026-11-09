FROM ghcr.io/devbykidle/axiom-runtime-container-pro:jdk-21.0.2_14-glibc

LABEL org.opencontainers.image.source='https://github.com/strimzi/strimzi-kafka-operator'

ARG strimzi_version

LABEL name='base' \
    vendor='Strimzi' \
    version="${strimzi_version}" \
    release="${strimzi_version}" \
    summary='Base image of the Strimzi Kafka Operator project.' \
    description='Strimzi Kafka Operator base image, containing common utilities across all component images.'

USER root

# The vendor image still points at the old pkg.axiomjdk.ru host, which now
# answers HTTP 401 ("private repository"). The current public Axiom Linux
# repository is served from packages.axiomjdk.ru.
RUN sed -i 's|pkg\.axiomjdk\.ru|packages.axiomjdk\.ru|g' /etc/apk/repositories

# Устанавливаем необходимые пакеты для работы и распаковки.
# `tini` is installed from the APK repository: the binaries published in the
# upstream GitHub release are glibc-linked and fail with "not found" on musl.
RUN apk update && apk upgrade --no-cache
# `netstat` and `nc` are provided by busybox in this image.
RUN apk add --no-cache \
      openssl \
      shadow \
      unzip \
      curl \
      bash \
      tzdata \
      gettext \
      findutils \
      grep \
      sed \
      gawk \
      tar \
      tini \
    && cp /usr/share/zoneinfo/UTC /etc/localtime \
    && echo "UTC" > /etc/timezone \
    && ln -sf /sbin/tini /usr/bin/tini
