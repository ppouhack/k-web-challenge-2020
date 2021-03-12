FROM    ubuntu:18.04
RUN \
    cd /etc/apt && \
    sed -i 's/archive.ubuntu.com/mirror.kakao.com/g' sources.list

RUN \
    apt-get update && \
    apt-get install -y -qq wget && \
    apt-get install -y -qq libpcre3 libpcre3-dbg libpcre3-dev build-essential libpcap-dev   \
                           libnet1-dev libyaml-0-2 libyaml-dev pkg-config zlib1g zlib1g-dev \
                           libcap-ng-dev libcap-ng0 make libmagic-dev libjansson-dev        \
                           libnss3-dev libgeoip-dev liblua5.1-dev libhiredis-dev libevent-dev \
                           mysql-server libmysqlclient-dev mysql-client autoconf libtool \
                           gcc g++ build-essential libssl-dev libreadline6-dev \
                           zlib1g-dev linux-headers-generic libsqlite3-dev \
                           libxslt1-dev libxml2-dev imagemagick git-core \
                           libmagickwand-dev default-jre ruby-full \
                           libpcre3 libpcre3-dbg libpcre3-dev build-essential automake \
                           tmux vim git sudo default-jdk gdebi-core \
                           libpcap-dev libnet1-dev libmysqlclient* 

RUN \ 
    cd /home && \
    wget https://www.openinfosecfoundation.org/download/suricata-4.1.0.tar.gz && \
    tar xzvf suricata-4.1.0.tar.gz && \
    cd suricata-4.1.0 && \
    ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var && \
    make && \
    make install

COPY . /home

RUN \
    mkdir -p /etc/suricata && \
    mkdir -p /var/lib/suricata/rules && \
    mkdir -p /var/log/suricata && \
    mkdir -p /var/log/barnyard2 && \
    mkdir -p /etc/barnyard2 && \
    mkdir -p /etc/data

RUN \
    mv /home/suricata-4.1.0/classification.config /etc/suricata/classification.config && \
    mv /home/suricata-4.1.0/reference.config /etc/suricata/reference.config && \
    mv /home/suricata.yaml /etc/suricata/suricata.yaml && \
    echo 'alert icmp any any -> any any (msg:"icmp"; sid:1; rev:1;)' > /var/lib/suricata/rules/suricata.rules && \
    mv /home/suricata-4.1.0/threshold.config /etc/suricata/threshold.config
  
RUN \    
    cd home && \
    tar zxvf barnyard.tar.gz && \
    cd barnyard2-2-1.13 && \
    ./autogen.sh && \
    ./configure --with-mysql --with-mysql-libraries=/usr/lib/x86_64-linux-gnu --with-mysql-includes=/usr/include && \
    make && make install 

RUN \
    mv /home/barnyard2.conf /etc/barnyard2/barnyard2.conf && \
    mv /home/barnyard2.waldo /etc/barnyard2/barnyard2.waldo && \
    mv /home/gen-msg.map /etc/barnyard2/gen-msg.map && \
    mv /home/sid-msg.map /etc/barnyard2/sid-msg.map  

RUN \
    service mysql start && \
    mysql -e "create database snorby CHARACTER SET utf8;" && \
    mysql -e "create user 'snorbyuser'@'localhost' IDENTIFIED BY 'sniper';" && \
    mysql -e "grant all privileges on *.* to snorbyuser@'localhost' identified by 'sniper' with grant option;" && \
    mysql -e "flush privileges;" && \
    mysql -usnorbyuser -psniper snorby < /home/create_mysql

RUN \
    wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.2.0-amd64.deb && \
    wget https://s3.amazonaws.com/files.molo.ch/builds/ubuntu-18.04/moloch_2.4.2-1_amd64.deb && \
    wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.2.0-amd64.deb && \
    sudo dpkg -i elasticsearch* && \
    sudo dpkg -i filebeat*

RUN \
    sudo gdebi -n moloch*

RUN \
    mv /home/elasticsearch.yml /etc/elasticsearch/elasticsearch.yml && \
    mv /home/filebeat.yml /etc/filebeat && \
    chown -R root:elasticsearch /etc/elasticsearch/elasticsearch.yml

RUN \
    mv /home/Seoul /etc/localtime

