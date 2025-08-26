---
title: "Kafka 연습"
date: 2024-06-27T06:30:40.848Z
tags: ["kafka"]
slug: "Kafka-Test"
thumbnail: "../assets/posts/0623aef5f380f34fd61ffaa6d51bed061feff91f6b30372bc48dd4d585fd6873.PNG"
categories: 공부
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:31.831Z
  hash: "db2712db7d66f84ed655a57d4dd4e2aa5f6bb71f76609c046f115a3514a5b1f9"
---

## Kafka 연습

mac에서 혼자 kafka를 연습해보았다

참고 링크 : https://ojava.tistory.com/205
		  https://devocean.sk.com/experts/techBoardDetail.do?ID=163709
          + 오류 구글링
          
`카프카 브로커 실행중인지 확인`
```          
ps -ef | grep kafka
```


`카프카 브로커 실행`
```
kafka_2.12-3.7.0/bin/kafka-server-start.sh kafka_2.12-3.7.0/config/server.properties
```

이후 토픽 만들고,


`Kafka의 콘솔 컨슈머를 사용하여 로컬 호스트에서 실행 중인 Kafka 서버의 localhost:9092에 연결하고, kguswo-test01 토픽에서 메시지를 소비하도록 지정하는 명령어`
```
kafka_2.12-3.7.0/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic kguswo-test01
```

`메시지 생산`
```
kafka_2.12-3.7.0/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic kguswo-test01
```

`메시지 소비`
```
kafka_2.12-3.7.0/bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic kguswo-test01 --from-beginning
```

`토픽 목록 조회`
```
~/kafka_2.12-3.7.0/bin/kafka-topics.sh --bootstrap-server localhost:9092 --list
```

`kguswo-test01이라는 토픽 생성`
```
~/kafka_2.12-3.7.0/bin/kafka-topics.sh --bootstrap-server localhost:9092 --topic kguswo-test01 --partitions 1 --replication-factor 1 --create
```

`토픽 삭제`
```
~/kafka_2.12-3.7.0/bin/kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic kguswo-test01
```

### 결과화면
![카프카 테스트 터미널](/assets/posts/3b34cbb3104c744fd007b9f2d8389868ae9645c450ca93c17f38632668741df5.png)
![](/assets/posts/a24792e16aee3e1efba2bc7a943b7ff01578c70b98e96e03b2f8a0698e761ee7.PNG)





