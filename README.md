# k-web-challenge

k-web-challenge 대회에 참가하여 구성한 Threat Intelligence 환경입니다.

공격을 탐지하고 이를 수집할 수 있는 Threat Intelligence 환경을 오픈소스를 활용하여 구축합니다.

![alt text](https://raw.githubusercontent.com/ppouhack/k-web-challenge-2020/master/sbm.png)

Threat Intelligence 환경은 다음의 순서로 동작합니다.

- moloch : 본 서버로 접근하는 모든 네트워크 트래픽을 저장합니다.
- Suricata : 네트워크 트래픽 중 악의적인 트래픽을 suricata 정책으로 탐지합니다.
- Barnyard2 : Suricata 정책에 의해 탐지된 트래픽을 Barnyard2를 통해 데이터를 저장합니다.
- Snorby : Branyard2를 통해 저장된 데이터는 Snorby를 사용하여 접근할 수 있습니다.
- Filebeat : Suricata 정책에 의해 탐지된 로그를 Elastic stack서버에 전달합니다.
- Elastic stack : Threat Intelligence 환경에서 발생한 로그를 Elastic stack에서 통합 분석합니다.

## Tech

프로젝트는 다음과 같은 오픈소스를 사용하여 구성하였습니다.

- suricata
- barnyard2
- Snorby
- Moloch
- ELK Stack
- GCP
- Docker

## Install

### suricata, barnyard2, Snorby, Moloch

호스트 환경에서 다음의 명령어를 입력합니다.

```bash
# vi /etc/sysctl.conf
vm.max_map_count=262144

# docker build -t theat_intelligence_server . 
# docker run -it --name ti1 --net=host --cap-add=net_admin \
#    -v /home/data:/etc/data \
#    threat_intelligence_server /bin/bash
```

### Elastic Stack

호스트 환경에서 다음의 명령어를 입력합니다.

```bash
# vi /etc/sysctl.conf
vm.max_map_count=262144

# docker run -it --name elk --net=host --cap-add=net_admin \
#     -v /home/data:/etc/data elk /bin/bash
```

## Usage

### suricata, barnyard2, Snorby, Moloch

Docker Container에서 다음의 명령어를 입력합니다.

```bash
# service mysql start

# /usr/bin/suricata -USR2 -c /etc/suricata/suricata.yaml -i enp5s0f0 &

# /usr/local/bin/barnyard2 -c /etc/barnyard2/barnyard2.conf -d /var/log/suricata -f sniper.alert -w /etc/barnyard2/barnyard2.waldo &

# service elasticsearch start

# /data/moloch/bin/Configure
> enp5s0f0 
> no
> enter
> no-default
> GEO -> yes

# /data/moloch/db/db.pl http://localhost:9200 init

> INIT

# /data/moloch/bin/moloch_add_user.sh admin admin "sniper" --admin

# cd /data/moloch/viewer
# /data/moloch/bin/moloch-capture -c /data/moloch/etc/config.ini  >> /data/moloch/logs/capture.log 2>&1 &
# /data/moloch/bin/node viewer.js -c /data/moloch/etc/config.ini  >> /data/moloch/logs/viewer.log 2>&1 &

# filebeat modules enable suricata
# filebeat setup -e
# service filebeat start
```

### Elastic Stack

Docker Container에서 다음의 명령어를 입력합니다.

```bash
# service elasticsearch start
# service kibana start
# service logstash start
```

## Result

### suricata, barnyard2, Snorby

![alt text](https://raw.githubusercontent.com/ppouhack/k-web-challenge-2020/master/suricata.png)

suricata에서 IPS 정책에 의해 탐지된 로그입니다.

### Moloch

![alt text](https://raw.githubusercontent.com/ppouhack/k-web-challenge-2020/master/moloch.png)

Moloch에 의해 본 서버로 전달되는 트래픽을 저장합니다.

### Elastic Stack

![alt text](https://raw.githubusercontent.com/ppouhack/k-web-challenge-2020/master/elk.png)

suricata에서 탐지된 로그를 filebeat를 통해 Elastic Stack에 전달하고, Kibana로 시각화 합니다.
