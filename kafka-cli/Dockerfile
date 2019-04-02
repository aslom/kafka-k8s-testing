FROM java:8
MAINTAINER Nicholas Johns <nicholas.a.johns5@gmail.com>
# based on https://github.com/taion809/kafka-cli


#ENV KAFKA_VERSION=1.0.1
ENV KAFKA_VERSION=2.2.0
#http://mirrors.sonic.net/apache/kafka/2.1.0/kafka_2.11-2.1.0.tgz
ENV KAFKA_URL=https://www-eu.apache.org/dist/kafka/${KAFKA_VERSION}/kafka_2.11-${KAFKA_VERSION}.tgz
#             https://www-eu.apache.org/dist/kafka/2.2.0/kafka_2.11-2.2.0.tgz
#ENV KAFKA_URL=http://mirrors.sonic.net/apache/kafka/2.1.0/kafka_2.11-2.1.0.tgz

ENV KAFKA_TMP_DEST=/opt/kafka.tgz
ENV KAFKA_WORKDIR=/opt/kafka

#ADD run.sh /opt/run.sh
#ADD test.sh /opt/test.sh
#RUN chmod +x /opt/run.sh && \
RUN wget $KAFKA_URL -O ${KAFKA_TMP_DEST} && \
    mkdir -p ${KAFKA_WORKDIR} && \
    tar -xvzpf ${KAFKA_TMP_DEST} --strip-components=1 -C ${KAFKA_WORKDIR}

ENV PATH="/opt/kafka/bin:${PATH}"

WORKDIR [ "/opt/kafka" ]
CMD [ "/bin/bash" ]
