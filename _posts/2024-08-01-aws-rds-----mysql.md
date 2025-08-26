---
title: "AWS RDS 설정 - 프리티어, MySQL"
description: "먼저 아래 링크에서 루트 사용자로 로그인 하자AWS RDS좌측 데이터베이스를 클릭하자데이터베이스RDS 인스턴스를 생성하기 위해 데이터베이스 생성을 클릭하자필자는 범용적인 설정을 위해 표준생성 해주었다. 손쉬운 설정으로 기본에 커스텀하고싶으면 오른쪽을 선택하자다음으로 사"
date: 2024-08-01T02:23:34.754Z
tags: ["aws","ec2","rds"]
slug: "AWS-RDS-설정-프리티어-MySQL"
thumbnail: "/assets/posts/e8824add4df28fc455150b0b7415f4face17bf6ddda8964e7ba4e5990802c49e.png"
categories: 프로젝트
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:14.896Z
  hash: "d172eec2a06797ff9473234f354b8b4f9c67794f04a0d1d14ab8e712ce7ec34a"
---

#### 프로젝트를 EC2 서버를 사용해 배포하며 DB정보도 RDS를 통해 분산형 관계형 데이터베이스 서비스를 사용하는 김에 정리하게 되었다.

> # RDS 설정

먼저 아래 링크에서 루트 사용자로 로그인 하자
[AWS RDS](https://ap-northeast-2.console.aws.amazon.com/rds/home?region=ap-northeast-2)

좌측 데이터베이스를 클릭하자
![데이터베이스](/assets/posts/e8824add4df28fc455150b0b7415f4face17bf6ddda8964e7ba4e5990802c49e.png)

RDS 인스턴스를 생성하기 위해 데이터베이스 생성을 클릭하자
![](/assets/posts/842c715003d033da5b64e8e332b05d5e034d28334e2b016b7e6f896d6b739eae.png)

필자는 범용적인 설정을 위해 표준생성 해주었다. 손쉬운 설정으로 기본에 커스텀하고싶으면 오른쪽을 선택하자![](/assets/posts/f31ef6ab7a0a73d5724469ef31591a4cb4c9ea7671bf7f1bfe37bc3c6f04bd45.png)

### 엔진 옵션
다음으로 사용하는 엔진 유형과 버전을 선택하자. 필자는 MySQL을 사용하고 있어 선택해 주었으며 버전도 선택하였다. 
다만 인텔리제이에서 DataGrip을 사용중인데 자동으로 데이터베이스 드라이버를 다운로드 해 가장 최신 버전을 사용중이라면 일치하는 버전이 없을수도 있다. 
하지만 일반적으로 MySQL의 마이너 버전 간에는 큰 차이가 없고, 호환성 문제도 거의 발생하지 않아 큰 문제는 없을것이다.
![](/assets/posts/7cc56cb31eaa7f8f468b41033fafb3d9f15e19b5752d967209d8ff2110622510.png) ![](/assets/posts/55bd882b1bcada7f38d44735fded4779c511a6d83d9a9121a7313ddf08a09c0a.png)

### 템플릿
다음으로 템플릿을 선택해주자. 프리티어 사용중이라면 프리티어 선택하는것을 권장한다. 
아마 프리티어 선택 시 아래 가용성에서 단일 DB 인스턴스가 고정될 것이다.
![RDS 템플릿](/assets/posts/447bf3bbc8a1a07e85eb113c908c8af02eb397d305fade0490a73b53c8d90c4c.png)

### 설정
DB 인스턴스 설정으로 DB 인스턴스 식별자와 자격 증명 설정을 하도록 하자. 식별자 이름을 작성하고 필요하다면 암호를 만들면 된다.  
필자는 자체 관리로 마스터 암호를 생성했다. - _비밀번호는 기록이나 기억해 두도록 하자_![](/assets/posts/e61b8ec90f153a4d46bd4597b2e87e46caf6e5846dc7186e6d1ba2930b23e491.png)


### 스토리지
프리티어를 선택했기 때문에 인스턴스 클래스는 버스터블 클래스(t클래스 포함)으로 자동 선택된 것이다. 
해당 인스턴스 클래스 중 과금 방지를 위해 db.t2.micro를 선택했다.
스토리지 자동 조정 역시 과금 방지를 위해 체크를 해제하였다.
프리티어 지원 클래스에 db.t2.micro, db.t3.micro, db.t4g.micro가 있다.


_**스토리지 설정에서 과금 방지를 위해 프리티어 최대 사용량이 20GiB이므로 20GiB으로 변경하고 스토리지 자동 조정도 꼭 꺼주자. **_
![](/assets/posts/61fc29c6e888de36adb957e6dab717f670bce69d4ad1682b29e8886bed4dd6f0.png)

### 연결
연결 설정 에서는 퍼블릭 액세스를 활성화하기 위해 EC2 컴퓨팅 리소스에 연결 안 함 을 선택한다. 나중에 VPC설정으로 EC2서버에서 RDS 접속 가능하다.
퍼블릭 엑세스는 예를 누르고 VPC보안그룹은 특정 그룹에 해당하는 경우에만 RDS 쓸 수 있게 하는 것이므로 새롭게 그룹 만들고 이름 작성하자. ~~_database가 무난하다
추가구성의 포트는 MySQL 기본포트 3306으로 설정했다.
![](/assets/posts/77f4fe8c9c694d743020197505589782a7ecc6f331975a4ae18116477429c058.png) ![](/assets/posts/231b651685fbc5147ff9e8c06d0ba29c5f9da895b6e67eb19802af04a9b08728.png)


태그 및 데이터베이스 인증은 편한대로 설정하자
![](/assets/posts/b31bc87964fb5bfd534565cf907f6cf5839ec4f8ceb601190e8ada0c7a71034f.png)

### 추가 구성
데이터베이스 추가 구성에서 20GB이상 스냅샷이 생성되면 프리티어라도 결제될 수 있기 때문에 자동 백업을 비활성화하자.
![](/assets/posts/e502e5b0d1082f1e2cfb25e64e269ea18786ed18aff0730176c961633e871f82.png) ![](/assets/posts/66a644f2579d1f8d7847ad4205f6483e789e2a25fe2d384b07dee0727e4c2579.png)


### 월별 추정 비용
이렇게 적혀는 있는데 프리티어 한도 내에서 사용하면 과금되지 않으니 그 사용량 내에서 작업하도록 항상 체크하자
![](/assets/posts/7361c41088b7cf30f5c92333a5cf520b476abaf676b7c001dd9a8dec34fc2702.png)

### 생성 후 확인하기
![](/assets/posts/768f3fd4e05a0b3450acdf924c1d0ebbfcdd6ab920fcc93564db093b279ef72c.png)![](/assets/posts/9b5780cc1307a8ee31e298e7bd60ff9380dafcc722e7937d3844aa2833763b4e.png)![](/assets/posts/03562c4201e020a56c54db952a706e08fa3a951a0401bebce2ee47aff414c09a.png)

> # 보안그룹 설정

서버에서 DB 접속 가능하게 하기 위한 설정
연결 및 보안에서 보안 그룹 규칙을 보면 Inbound에 자신의 로컬IP, Outbound는 모든 트래픽인 0.0.0.0/0일 것이다. 여기서 현재 보안 그룹 확인하자.
Inbound 규칙을 선택 클릭하고 인바운드 규칙 편집.![](/assets/posts/e8b9dca0f5f3625008cc9c1fa9e02ab2cafb4a442e3897b53a35570d97997939.png)![](/assets/posts/8a7fa20f6a03368fd9e15cc8fd34f7856846e34fcf4d35ea21f3bcf20fec76cc.png)
아래와 같이 인바운드 규칙 추가하자
![](/assets/posts/d821f7338cfd9405b6103622cf86dda46f7cda1a09d49a1fcd3da58e24a579c9.png)

> MySQL과 연결 및 DB, 테이블 생성

MySQL WorkBench사용해서 접속한 뒤 만들어보겠다. 위 Database에서 아까 생성 시 기억해야했던 정보들이 필요하다.
- Hostname : 엔드포인트
- Username : 마스터 아이디, 필자는 admin
- Password : 마스터 암호

![](/assets/posts/1044be81dc00c09e8a80c9f8b05d8832b8e7396c560b1b07d453454ea127a7b6.png)![](/assets/posts/4a0b452a91da2097eea75b73bd3e2b937c174c7dd2c5fd99b6a3f2e518711a0f.png)
이렇게 생성 후 제대로 접속이 된다면 성공이다. 테스트를 진행해보자. 
![](/assets/posts/2a1bdd4a68be4b2058760d589e28b2c28e019053fa2dc502f737d86af177d54e.png)![](/assets/posts/bdc07a33db2bd15909cfe0a3a34f11ddbddc2d690d33c3587361eb6605d9db96.png)

인텔리제이에서 연동하여보자
![](/assets/posts/e6f6c7dafa6619487bc8d5bf86613b3e652596debdb8dab1bc2302548d11a509.png)![](/assets/posts/b48446c7c2a43b572ae1771b83ecbbdee8f76ed2eeb9dc30373fcee8fe908829.png)

정상적으로 연결되었으며 잘 작동한다.

이 배포용 DB는 yml에서 연결정보를 다시 적어주어야한다.
```java
  datasource:
    url: jdbc:mysql://(엔드포인트 추가 괄호는 제거)
    username: 사용자 이름 (ex : admin)
    password: 비밀번호 추가
    driver-class-name: com.mysql.cj.jdbc.Driver
```













> # 파라미터 그룹

timezone, 인코딩 등 DB 인스턴스 적용 옵션을 지정할 수 있다

![](/assets/posts/16c960d3c006675b69ca98b0b35e274641ec96ca41e3db658edeb65a07fb2934.png)![](/assets/posts/417dbe53983c85c88d42129cbd0447752490bb3ec62ac3296e99a28afcf4e03d.png)
편집에 들어가자
![](/assets/posts/e133646c58a9e858dc80ea31764c5c26d6214cbb633532acf723f2e4cc7bc2c8.png)

1. **character**
검색창에 character를 입력하고 아래 체크된 6개 파라미터의 값을 utf8mb4 로 작성후 변경사항 저장![](/assets/posts/abf30b0bfa987b9690e9cd39548b8255ac027c0e317a41dda418fd23d10c30ab.png)

 **Why?**
MySQL, MariaDB는 utf8로 세팅하는 경우 이모지(ex: 😄)문자가 입력되지 않아서 utf8mb4 라는 charset을 사용 .
이모지문자를 표현하기 위해서 글자당 최대 4bytes가 필요하지만 utf8에서는 가변3바이트를 사용하기 때문에 이모지문자가 깨지므로 4바이트를 사용하는 utf8mb4로 설정한다.

**2. Time Zone**
검색창에 time_zone 입력하고 Asia/Seoul 로 작성후 변경사항 저장
![](/assets/posts/39a5a5d1fad4200e9ff4399822fb3503f3010e56da42619c4db1952d5dd0521b.png)

**3. collation** 
검색창에 collation 입력하고 아래 2개 파라미터의 값을 utf8mb4_general_ci 로 작성후 변경사항 저장

설명
- utf8mb4_general_ci는 MySQL에서 사용하는 문자 집합과 정렬 규칙의 조합입니다. 이 설정은 다음과 같은 의미를 가지고 있습니다:

  - utf8mb4: 이는 MySQL의 문자 집합으로, 유니코드 문자 집합의 4바이트 버전입니다. utf8mb4는 utf8보다 더 넓은 범위의 유니코드 문자를 지원하여, 이모지와 같은 4바이트 문자를 포함한 다양한 문자를 저장할 수 있습니다.

  - general: 이 부분은 문자 정렬 규칙을 나타냅니다. general은 일반적인 정렬 규칙을 사용하며, 대소문자 구분이 없는 비교를 지원합니다.

 - ci: 이는 "case-insensitive"의 약자로, 대소문자를 구분하지 않는다는 의미입니다. 즉, A와 a는 동일한 문자로 간주됩니다.
 
따라서 utf8mb4_general_ci는 유니코드 문자 집합을 사용하고, 대소문자를 구분하지 않으며, 일반적인 정렬 규칙을 따르는 설정입니다. 이 설정은 대부분의 경우에 적합하며, 다양한 언어와 특수 문자를 처리할 수 있는 유연성을 제공
![](/assets/posts/f40a05ad6de12723fa707e81ff4414c014b6a3a08be6caa9c8d449cfe2f95222.png)

**4. max_connections (선택사항)**
이 부분은 동시 클라이언트 연결 수 설정인데
MySQL 5.7: 기본값은 151
MySQL 8.0: 기본값은 151
라고 한다. 기본값이 낮을 경우 설정을 바꿀 수 있을 것 같다. 일단 패스

다 적용했으면 데이터베이스로 돌아가서 자신의 DB선택 후 수정 누르기.
![](/assets/posts/09799c681bf04045b40889f0fc499a9112899d08f83b306803814895fddea8ed.png)
아까 생성할 때 보았던 파트 중 추가 구성으로 내려가 
![](/assets/posts/c05e764ff2481e6417e26f20008fd757219ee60dc98ccf4ba1dc25c6a0e1c5f5.png)
이렇게 자신의 파라미터 그룹으로 변경 후 즉시 적용으로  수정
![](/assets/posts/ebb9b181a833c9a8c45a708805edfc14d302c78227fcee655d79a162833b7e68.png)



