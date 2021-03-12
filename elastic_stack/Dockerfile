FROM    ubuntu:18.04
RUN \
    cd /etc/apt && \
    sed -i 's/archive.ubuntu.com/mirror.kakao.com/g' sources.list

RUN \
    apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y default-jdk vim tmux wget

COPY . /home

RUN \
    wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.2.0-amd64.deb && \
    wget https://artifacts.elastic.co/downloads/kibana/kibana-7.2.0-amd64.deb && \
    wget https://artifacts.elastic.co/downloads/logstash/logstash-7.2.0.deb && \
    dpkg -i elasticsearch-7.2.0-amd64.deb && \
    dpkg -i kibana-7.2.0-amd64.deb && \
    dpkg -i logstash-7.2.0.deb

RUN \
    rm /etc/elasticsearch/elasticsearch.yml && \
    rm /etc/kibana/kibana.yml && \
    mv /home/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml && \
    mv /home/kibana.yml /etc/kibana/kibana.yml && \
    chown -R root:elasticsearch /etc/elasticsearch/elasticsearch.yml && \
    mv /home/logstash-weblog.conf /etc/logstash/conf.d && \                                   