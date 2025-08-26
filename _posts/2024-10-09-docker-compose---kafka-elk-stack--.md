---
title: "docker compose 로 Kafka + ELK stack 사용"
date: 2024-10-09T08:28:51.165Z
tags: ["ELK","docker","kafka"]
slug: "docker-compose-로-Kafka-ELK-stack-사용"
image: "../assets/posts/7cbec649485ed6380432c70c6fe86b95a4d0cb940e6a5d21725654c3bd405503.png"
categories: 프로젝트
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:52.687Z
  hash: "af3fc91bdb17160b2f6a43844331a60fbfa4148abbe24ef9d9e1892824077eaa"
---

> 여러 채용 공고 및 회사별 기술 스택을 보며 대부분 MSA아키텍처를 채택하며 이를 분석하는데 ELK Stack을 사용하는것을 보고 파이프라인을 구현해보았습니다.

## ELK 스택 파이프라인

먼저 전체적인 파이프라인 및 디렉토리 구조입니다. ( 터미널에서 `tree` 혹은 `tree -f` 로 볼 수 있음 )  
```bash
├── docker-elk-stack  						# ELK 스택을 위한 Docker 구성 디렉토리
│   ├── docker-compose.yml  				# 전체 ELK 스택 서비스 정의 및 구성
│   ├── elasticsearch  						# Elasticsearch 관련 파일
│   │   ├── Dockerfile  					# Elasticsearch 컨테이너 빌드 스크립트
│   │   └── config
│   │       └── elasticsearch.yml  			# Elasticsearch 설정 파일
│   ├── filebeat  							# Filebeat 관련 파일
│   │   └── config
│   │       └── filebeat.yml  				# Filebeat 설정 파일 (로그 수집 및 전송 구성)
│   ├── kibana  							# Kibana 관련 파일
│   │   ├── Dockerfile  					# Kibana 컨테이너 빌드 스크립트
│   │   └── config
│   │       └── kibana.yml  				# Kibana 설정 파일
│   └── logstash  							# Logstash 관련 파일
│       ├── Dockerfile  					# Logstash 컨테이너 빌드 스크립트
│       ├── config
│       │   ├── logstash.yml  				# Logstash 주 설정 파일
│       │   └── pipelines.yml  				# Logstash 파이프라인 정의 파일
│       └── pipeline
│           └── logstash.conf  				# Logstash 파이프라인 설정 파일
├── logs
│   ├── application.log  					# 애플리케이션 로그 파일 (Filebeat가 수집할 대상)

```
<br>

### 올바른 ELK stack + Kafka 흐름
1. 애플리케이션이 application.log에 로그를 작성합니다.
2. Filebeat가 application.log 파일을 읽습니다.
3. Filebeat가 로그 데이터를 Kafka(토픽: "OMG")로 전송합니다.
4. Logstash가 Kafka 토픽에서 데이터를 읽습니다.
5. Logstash가 로그 데이터를 처리(필터 적용)하고 Elasticsearch로 전송합니다.
6. Kibana가 Elasticsearch에서 로그 데이터를 읽어 시각화합니다.

일반적인 ELK 스택 구성과의 주요 차이점은 Filebeat와 Logstash 사이에 Kafka가 있다는 것입니다. 이 구성은 더 나은 확장성과 컴포넌트 간의 분리를 가능하게 합니다.

기본적으로 모두 도커로 띄워 설치관리하였습니다. 로컬에서만 테스트해볼 예정이라면 Docker Desktop 사용을 권장드립니다. 참고로 진행환경은 MacOS M3입니다.

다음으로 코드를 알아보겠습니다.

### application.yml
```
logging:
  file:
    name: /app/logs/application.log
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss} - %msg%n"
    file: "%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n"
  level:
    org.hibernate.SQL: debug
    com.ssafy.omg: debug


  kafka:
    producer:
      bootstrap-servers: kafka:9092
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
    consumer:
      bootstrap-servers: kafka:9092
      group-id: your-group-id
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer

``` 
로컬에서 테스트하신다면 kafka 설정 부분에서
localhost:9092로 바꾸면 됩니다. 

### docker-compose.yml
```java
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.11.1
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - xpack.security.enabled=true
      - xpack.security.authc.token.enabled=true
      - xpack.security.authc.api_key.enabled=true


    ports:
      - 9200:9200
      - 9300:9300
    volumes:
      - elasticsearch_data:/usr/share/elasticsearch/data
    healthcheck:
      test: [ "CMD-SHELL", "curl -s -u elastic:${ELASTIC_PASSWORD} http://localhost:9200 >/dev/null || exit 1" ]
      interval: 30s
      timeout: 10s
      retries: 5
    networks:
      - elk_network

  logstash:
    build:
      context: ./logstash
      dockerfile: Dockerfile
      args:
        ELK_VERSION: 8.11.1
    container_name: logstash
    ports:
      - 5001:5000
    volumes:
      - ./logstash/pipeline:/usr/share/logstash/pipeline
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
      - ./logstash/config/pipelines.yml:/usr/share/logstash/config/pipelines.yml
      - /home/ubuntu/logs:/usr/share/logstash/logs
    depends_on:
      elasticsearch:
        condition: service_healthy
      kafka:
        condition: service_started
    environment:
      - SLACK_WEBHOOK_URL=${SLACK_WEBHOOK_URL}
      - LANG=en_US.UTF-8
      - LC_ALL=en_US.UTF-8
      - LS_JAVA_OPTS=-Xmx1g -Xms1g
      - ELASTIC_USERNAME=elastic
      - ELASTIC_PASSWORD=${ELASTIC_PASSWORD}
      - LOG_LEVEL_DEBUG
    healthcheck:
      test: [ "CMD", "curl", "-f", "http://localhost:9600" ]
      interval: 30s
      timeout: 10s
      retries: 5
    networks:
      - elk_network


  kibana:
    image: docker.elastic.co/kibana/kibana:8.11.1
    container_name: kibana
    ports:
      - 5601:5601
    depends_on:
      elasticsearch:
        condition: service_healthy
    environment:
      - ELASTICSEARCH_HOSTS=http://elasticsearch:9200
      - ELASTICSEARCH_SERVICEACCOUNTTOKEN=${KIBANA_SERVICE_TOKEN}
      - ELASTICSEARCH_SSL_VERIFICATIONMODE=none
    networks:
      - elk_network


  zookeeper:
    image: wurstmeister/zookeeper
    container_name: zookeeper
    ports:
      - 2181:2181
    networks:
      - elk_network


  kafka:
    image: wurstmeister/kafka
    container_name: kafka
    ports:
      - "9092:9092"
      - "9093:9093"
    volumes:
      - /home/ubuntu/logs:/logs
    environment:
      KAFKA_ADVERTISED_LISTENERS: INSIDE://kafka:9092,OUTSIDE://localhost:9093
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: INSIDE:PLAINTEXT,OUTSIDE:PLAINTEXT
      KAFKA_LISTENERS: INSIDE://0.0.0.0:9092,OUTSIDE://0.0.0.0:9093
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_INTER_BROKER_LISTENER_NAME: INSIDE
      KAFKA_NUM_PARTITIONS: 3
      KAFKA_LOG_FLUSH_INTERVAL_MESSAGES: 10000
      KAFKA_LOG_FLUSH_INTERVAL_MS: 1000
    depends_on:
      - zookeeper
    networks:
      - elk_network


  filebeat:
    image: docker.elastic.co/beats/filebeat:8.11.1
    container_name: filebeat
    volumes:
      - ./filebeat/config/filebeat.yml:/usr/share/filebeat/filebeat.yml
      - /home/ubuntu/logs:/logs
    command: [ "filebeat", "-e", "-strict.perms=false" ]
    depends_on:
      - kafka
      - logstash
    networks:
      - elk_network


volumes:
  elasticsearch_data:
    driver: local


networks:
  elk_network:
    driver: bridge

```
마찬가지로 로컬 환경에서 테스트 하신다면 volume에서 마운트 하는 경로를 /home/ubuntu 부분을 ../logs등으로 바꾸시면됩니다.

---
## Filebeat
#### filebeat.yml
```java
filebeat.inputs:
  - type: log
    paths:
      - /logs/*.log
    close_inactive: 5s
    scan_frequency: 1s

output.kafka:
  hosts: [ "kafka:9092" ]
  topic: "OMG"
  partition.round_robin:
    reachable_only: false
  required_acks: 1
  compression: gzip
  max_message_bytes: 1000000
  codec.json:
    pretty: false

processors:
  - add_host_metadata: ~
  - add_cloud_metadata: ~

queue.mem:
  events: 1024
  flush.min_events: 10
  flush.timeout: "100ms"
```

---
## Kafka
카프카는 이벤트 메시지 브로커로 도커로 이미지를 실행하여 진행했습니다.
차후 실행 방법과 함께 다루도록 하겠습니다.

---

## logstash
#### logstash.yml
```java
## Default Logstash configuration from Logstash base image.
## https://github.com/elastic/logstash/blob/master/docker/data/logstash/config/logstash-full.yml
#
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "elasticsearch:9200" ]
xpack.monitoring.enabled: true
xpack.monitoring.elasticsearch.username: "elastic"
xpack.monitoring.elasticsearch.password: ${ELASTIC_PASSWORD}
xpack.monitoring.collection.pipeline.details.enabled: true
#xpack.monitoring.elasticsearch.api_key: null
#xpack.monitoring.elasticsearch.data_stream: true
pipeline.ordered: false
pipeline.workers: 2
pipeline.batch.size: 125
pipeline.batch.delay: 50

log.level: debug
```
#### pipelines.yml
```java
- pipeline.id: main
  path.config: "/usr/share/logstash/pipeline/*.conf"
```

#### logstash.conf
```java
input {
  kafka {
    bootstrap_servers => "kafka:9092"
    topics => ["OMG"]
    group_id => "logstash"
    auto_offset_reset => "latest"
    consumer_threads => 3
    decorate_events => true
  }
}

filter {
    json {
        source => "message"
        target => "parsed_json"
        skip_on_invalid_json => true
    }

    if [parsed_json] {
        mutate {
            rename => { "[parsed_json][message]" => "log_entry" }
        }
    } else {
        mutate {
            rename => { "message" => "log_entry" }
        }
    }

    grok {
        match => {
            "log_entry" => [
                "%{TIMESTAMP_ISO8601:timestamp} \[%{DATA:thread}\] %{LOGLEVEL:log_level} %{DATA:logger} - %{GREEDYDATA:log_message}",
                "%{TIMESTAMP_ISO8601:timestamp} \[%{DATA:thread}\] %{LOGLEVEL:log_level} %{GREEDYDATA:log_message}",
                "%{GREEDYDATA:log_message}"
            ]
        }
    }

    date {
        match => [ "timestamp", "yyyy-MM-dd HH:mm:ss", "yyyy-MM-dd HH:mm:ss.SSS", "ISO8601" ]
        target => "@timestamp"
    }

    if [log_level] == "ERROR" {
        mutate {
            add_field => {
                "error_type" => "application_error"
                "slack_message" => "🚨 *에러 발생* 🚨\n*Timestamp:* %{timestamp}\n*Thread:* %{thread}\n*Logger:* %{logger}\n*Message:* %{log_message}"
            }
        }
    }
}

output {
    if [log_level] == "ERROR" {
        http {
            url => "${SLACK_WEBHOOK_URL}"
            http_method => "post"
            content_type => "application/json"
            format => "message"
            message => '{"text":"%{slack_message}"}'
            request_timeout => 60
            retry_failed => true
            retryable_codes => [500, 502, 503, 504]
        }
        elasticsearch {
            hosts => ["http://elasticsearch:9200"]
            index => "omg-game-error-log-%{+YYYY.MM.dd}"
            user => "${ELASTIC_USERNAME}"
            password => "${ELASTIC_PASSWORD}"
        }
    } else {
        elasticsearch {
            hosts => ["http://elasticsearch:9200"]
            index => "omg-game-log-%{+YYYY.MM.dd}"
            user => "${ELASTIC_USERNAME}"
            password => "${ELASTIC_PASSWORD}"
        }
    }
}
```
http output 부분은 slack 에러 수신 웹훅 메시징도 추가한 부분입니다.

#### Dockerfile
```java
ARG ELK_VERSION

# https://www.docker.elastic.co/
FROM docker.elastic.co/logstash/logstash:${ELK_VERSION}

USER root

RUN apt-get update && apt-get install -y locales
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# Add your logstash plugins setup here
# Example: RUN logstash-plugin install logstash-filter-json
RUN logstash-plugin install logstash-output-http

COPY config/logstash.yml /usr/share/logstash/config/logstash.yml
COPY config/pipelines.yml /usr/share/logstash/config/pipelines.yml
COPY pipeline /usr/share/logstash/pipeline

USER logstash

CMD ["logstash", "-f", "/usr/share/logstash/pipeline/logstash.conf", "--config.reload.automatic"]

```
---
## elasticsearch
#### elasticsearch.yml
```java
## Default Elasticsearch configuration from Elasticsearch base image.
## https://github.com/elastic/elasticsearch/blob/master/distribution/docker/src/docker/config/elasticsearch.yml
#
cluster.name: "docker-cluster"
network.host: 0.0.0.0

## X-Pack settings
## see https://www.elastic.co/guide/en/elasticsearch/reference/current/setup-xpack.html
#
xpack.license.self_generated.type: trial
xpack.security.enabled: true
xpack.security.authc.token.enabled: true
xpack.monitoring.collection.enabled: true
discovery.type: single-node
index.refresh_interval: "1s"
```
#### Dockerfile
```java
ARG ELK_VERSION

# https://www.docker.elastic.co/
FROM docker.elastic.co/elasticsearch/elasticsearch:${ELK_VERSION}

# Add your elasticsearch plugins setup here
# Example: RUN elasticsearch-plugin install analysis-icu
```

---
## kibana
```java
## Default Kibana configuration from Kibana base image.
## https://github.com/elastic/kibana/blob/master/src/dev/build/tasks/os_packages/docker_generator/templates/kibana_yml.template.js
#
server.name: kibana
server.host: "0"
elasticsearch.hosts: [ "http://elasticsearch:9200" ]
xpack.monitoring.ui.container.elasticsearch.enabled: true
xpack.security.encryptionKey: "${KIBANA_ENCRYPTION_KEY}"
xpack.encryptedSavedObjects.encryptionKey: "${KIBANA_ENCRYPTION_KEY}"
xpack.reporting.encryptionKey: "${KIBANA_ENCRYPTION_KEY}"

## X-Pack security credentials
elasticsearch.serviceAccountToken: "${KIBANA_SERVICE_TOKEN}"
elasticsearch.requestTimeout: 30000
elasticsearch.ssl.verificationMode: none


```
password로 인증하는 방법도 있고 encryption키 토큰으로 인증하는 방법도 있는데 전 더 선호되는 기술을 사용해봤습니다.

#### Dockerfile
```java
ARG ELK_VERSION

# https://www.docker.elastic.co/
FROM docker.elastic.co/kibana/kibana:${ELK_VERSION}


# Add your kibana plugins setup here
# Example: RUN kibana-plugin install <name|url>
```
---

### 환경변수 .env
```java
ELASTIC_USERNAME=elastic
ELASTIC_PASSWORD=ssafya206
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/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

KIBANA_SERVICE_TOKEN=AAEAAWVsYXN0aWMva2liYW5hL2tpYmFuYS10b2tlbjp3cW02cFdSTlFsMlh5SDltakdzYjBn

```
이 환경변수 파일을 추가하는 방법도 아래 설명하겠습니다.

## 실행 방법

먼저 로컬에서 실행해봅시다.
Docker Desktop을 실행해봅시다. 물론 터미널에서도 진행 가능하지만 처음 여러 도커 이미지를 띄우고 실행하며, 종료됐을 때 docker logs [컨테이너]로 로그 분석 및 각 컨테이터 터미널을 쉽게 접속하려면 도커 데스크탑을 추천합니다. 물론 리눅스 개발 환경이 더 익숙하신 분들은 터미널로 똑같이 진행하시면 됩니다.![](/assets/posts/dcf06d826dd42c49e38b1a57660c16374fc5ff8a639d1ea33b2bcb60f8d968a7.png)


#### 1. docker-elk-stack 이 있는 루트 디렉토리로 이동
![](/assets/posts/10cf9255c074bb6e428bc63e801f812f810113f20efe2f33c43576ec0a9676fb.png)

#### 2. docker로 실행

1. Elasticsearch만 먼저 시작합니다
`docker-compose up -d elasticsearch`

2. Elasticsearch가 완전히 시작될 때까지 기다립니다.
서비스 계정 토큰을 생성하고 .env 파일에 설정합니다.
`docker exec -it elasticsearch /bin/bash -c "bin/elasticsearch-service-tokens create elastic/kibana kibana-token"`
3. 나머지 서비스를 시작합니다
`docker-compose up -d`
4. docker ps로 실행중인 컨테이너 확인합니다
`docker ps`

### 실행 후 로그 확인
각각 잘 실행되었는지, 뭔가가 exited되면 왜 종료되었는지를 확인하기 위해 logs를 봐야할 때가 있습니다. 이때는
```
docker-compose logs filebeat
docker-compose logs kafka
docker-compose logs logstash
docker-compose logs elasticsearch
docker-compose logs kibana
```
다음 5개 순서대로 플로우가 흘러가니 문제가 생긴 컨테이너의 터미널로 가 실행하여 오류를 로그를 통해 해결할 수 있습니다. 

카프카의 경우 토픽에 제대로 로그가 들어오는지를 확인해야 하는 경우가 있기 때문에 복잡한 명령어를 미리 적어두었습니다. 

#### 카프카 터미널 명령어 (서버의 경우 kafka:9092, 로컬의 경우 localhost:9092)
- Kafka 컨테이너에 접속하기 : 
`docker exec -it kafka /bin/bash`

- Kafka 스크립트 경로로 이동: 컨테이너에 접속한 후, Kafka 스크립트가 위치한 경로로 이동합니다. 일반적으로 Kafka의 스크립트는 /opt/kafka/bin에 위치합니다. : 
`cd /opt/kafka`

- 토픽 목록 확인: 이제 kafka-topics.sh 스크립트를 실행하여 현재 Kafka 클러스터의 토픽 목록을 확인할 수 있습니다. : 
`./bin/kafka-topics.sh --list --bootstrap-server localhost:9092`
![](/assets/posts/7f4cc758a08308e7636ff7b0bd7f31b0e289fcdb8782a8ccf650e9d8d89874e3.png)사진의 경우 바로 cd /opt/kafka/bin으로 이동해 진행한 모습

- 토픽 리스트 확인:
`./bin/kafka-topics.sh --list --bootstrap-server kafka:9092`

- 특정 토픽의 메시지 확인 (예: "OMG" 토픽):
`./bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic OMG --from-beginning`
이 명령어는 "OMG" 토픽의 모든 메시지를 처음부터 보여줍니다.
- 실시간으로 새로운 메시지만 보고 싶다면:
`./bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic OMG`

- 메시지와 함께 키, 타임스탬프 등을 보려면:
`./bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic OMG --property print.key=true --property print.timestamp=true --from-beginning`

- 특정 파티션의 메시지만 보려면 (예: 파티션 0):
`./bin/kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic OMG --partition 0 --from-beginning`

- 메시지 생산을 테스트 :
`./bin/kafka-console-producer.sh --bootstrap-server kafka:9092 --topic OMG`


---

## elastic 접속
localhost:5601(http://localhost:5601/
) 또는 도메인:5601(http://j11a206.p.ssafy.io:5601/
)로 접속합니다
이때 저희가 설정한 ELASTIC_USERNAME과 ELASTIC_PASSWORD로 로그인 합니다.
**https 면 접속이 안되니까 주의!**![](/assets/posts/7cbec649485ed6380432c70c6fe86b95a4d0cb940e6a5d21725654c3bd405503.png)![](/assets/posts/2242f1ff97addf10b1ff35d6c62948f3d7076ef8fcf12df07d110d8efdbd34a9.png)

### 인덱스 생성
좌측 탭에서 맨 아래 Stack Management로 들어간다. ![](/assets/posts/736a8e3ffa9991423e56045a6307e729a42cfeb0e1bd09c9b2bf826d6771d7f1.png)![](/assets/posts/bca6ac01296da6d63c24d58b932635905831f97d1c0355b7189b676ddb6e9aa5.png)
이후 다시 좌측 탭을 보면 여러 카테고리가 변경되는데 여기서 Index Management로 들어간다. ![](/assets/posts/bae162a23dd99f76b2b73e878f74ebad1f5da96805d1b8311626e8e0ffbabee5.png)

아마 아무것도 없고 새로 생성해야할텐데 이미 로그를 생성할 때 logstash의 output에서
`index => "omg-game-error-log-%{+YYYY.MM.dd}"`,           
`index => "omg-game-log-%{+YYYY.MM.dd}"`

와 같이 인덱스를 지정했으므로 해당 인덱스를 적어준다.
둘 다 포함하기 위해 omg-game*과 같이 적어주었다.

만약 인덱스를 모르겠으면 ![](/assets/posts/bca6ac01296da6d63c24d58b932635905831f97d1c0355b7189b676ddb6e9aa5.png) 에서 Dev Tools로 들어가 간단한 콘솔문으로 인덱스를 모두 출력해볼 수 있다. 
`GET _cat/indices?v`
![](/assets/posts/6bc95d7b6a1111746197ef2b5c9ceb52cc3e9d5384eca2d191dc1e6f43e32001.png)

## 로그 분석
이제 로그를 확인해 볼 차례다. 결국 이 단계를 위해 모든 과정을 거친것이다. 
![](/assets/posts/ef89c2710fb5c15191aa0477e3a52eb0f0fe92f5741a84b2763ed2a896120117.png)
Discover에 들어간다. 
![](/assets/posts/aa09762dd736077f0b11bab8d3a92291571d12ce754435f3c8aa4b0522658cf6.png)
이런식으로 분석 페이지 및 로그 내용이 나온다. 우리 서비스를 많이 이용했는지 로그데이터가 130만건이 쌓였다. 

어떤 로그가 있는지 보도록 해보자.
일단 우측상단 시간을 통해 원하는 시간대를 설정한다.![](/assets/posts/f07353dcbae22f8ed0c35066c21cd5c6dfefa2f1fe5e54fb0d8bbe67ce85fb9e.png)
이런식으로 설정을 한 뒤 원하는 조건을 걸어보자. message 중 에러가 있는걸 보도록 하자.
![](/assets/posts/2821fc1da5c89e954b900e696c15cba4e94cc712abcfb4557aa7a9789fbf6101.png)
이렇게 message : error과 같이 필터를 걸어주면 원하는 로그 데이터만 볼 수 있다. 분석해보니 
![](/assets/posts/2078815cb777020bca257f645833433dde0832b1951122c0078f956da721907d.png) 웹소켓 연결을 계속 시도하면서 에러가 발생했음을 알 수 있다. 이렇게 특정 에러를 분석하고 바로 대응할 수 있다. 

이에 특정 에러를 감지하여 수신 웹후크 앱을 통해 Slack으로 에러메시지를 실시간 전송하도록 추가했다. 
먼저 슬랙을 통해 ![](/assets/posts/dbed9f71bae26386a95a42f57b3152e10d12b2985be78b4b8c548b9b31a863b8.png) 자동화 앱을 선택한다.
![](/assets/posts/a7dce6827216149aecb225475d31ec0c1dce94b653bb4da82b84453a04401aaa.png)
우측 상단 Slack 마켓플레이스를 선택한다. 
![](/assets/posts/51c8df9c3f79a54caaad0092570371dce203e69c9a9c64903c772b2642d75925.png)
찾아보기에서 web을 검색해 수신 웹후크를 선택.
![](/assets/posts/5d8615f965a147afcbfb7e56070aab1fde08ca59a3e8f4af6ed6409913cbab27.png)
이후 사용자화 한 뒤 사용
![](/assets/posts/763339dc61b70ee752f0823cc6308d4fae686968209e1d02b94c3c54198a6c6e.png)![](/assets/posts/c60414cbea8f09f964df482f6802872136162e97952183e27e01a96a3034f85a.png)

생성된 웹후크 URL은 .env에 추가하면된다.

결과 화면은 다음과 같다.
![](/assets/posts/3c8a7f34f717e0ad82a8c014ab95364081f57aaa04390692aad230e23bf3f0b0.png)
