---
title: "Real MySQL - Ch 1 , 2"
date: 2025-04-26T08:14:26.714Z
tags: ["Database","RealMySQL 8.0"]
slug: "Real-MySQL-Ch-1-2"
image: "../assets/posts/88676672a92397567fb64965ab91aa5f2f1dc8a2fe4a3586600b0a42669b1a1b.png"
categories: 데이터베이스
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:01:21.735Z
  hash: "ea27476353aa7684f9229604488c5c25f427568109428ddf357394af6c159f52"
---

> # Ch 01. 소개

<br/><br/>

# 1.1 MySQL 소개
---

> MySQL은 1979년 스웨덴의 TcX라는 회사의 터미널 인터페이스 라이브러리인 UNIREG로부터 시작된다. 이후 MySQL 버전 1.0이 완성된 후 사내에서만 사용되다가, 1996년에 일반인에게 공개됐다.

<br/><br/>

# 1.2 왜 MySQL인가?
---

> ### 💡 **MySQL의 경쟁력은 가격이나 비용이다.** 

방대한 양의 데이터를 저장하기에 오라클 RDBS는 너무 비싸다.
MySQL도 100% 무료는 아니지만, 커뮤니티 에디션과 엔터프라이즈 에디션간의 큰 차이가 없기에 무료로 사용해도 큰 문제가 없다.

```
Q : 어떤 DBMS를 사용할 지 모르겠어요. 어떤 DBMS가 좋은가요?
```

이 질문에 대해 고민할 때 다음을 고려하자
 
**1. `안정성`**
**2. `성능과 기능`**
**3. `커뮤니티나 인지도`** - 필요한 경험이나 지식을 구할 수 있다.

가장 중요한 항목은 **안정성** 이다. 성능, 기능은 돈이나 노력으로 해결되지만 안정성은 그렇지 않다.
안정성 다음으로 성능과 기능을 고려하고, 그 다음으로 커뮤니티나 인지도를 고려해보자.

<br/>

[DB-Engines Ranking](https://db-engines.com/en/ranking) 사이트에서 DBMS 랭킹을 살펴보자. 상위 20개를 표와 차트로 나타내면 아래와 같다.
(점수 부여 기준 : 웹사이트 언급 횟수, 검색 빈도, 기술 토론 빈도, DBMS별 구인, 전문가 인맥)

<br/>

> ##### 2025년 4월, 424개 DBMS 시스템 중 상위 20개

![](/assets/posts/156818b2e12b796e62628c40aff19e658edeea689df044ab3cfa2b6f5854a614.png)
<br/>

> ##### 2025년 4월, DBMS 시스템 트렌드 차트

![](/assets/posts/dbec5bedd0f3d7c8782fb05dafcca5c0dd28c1aae77d33e95c15a924f38f6fa5.png)

<br/><br/>

> # 2. 설치와 설정


<br/><br/>

# 2.1 MySQL 서버 설치
---

> 💡 가능하다면 리눅스의 RPM이나 운영체제별 인스톨러를 이용하기를 권장

다양한 형태로 설치 가능한 MySQL 서버
- Tar 또는 Zip으로 압축된 버전
- 리눅스 RPM 설치 버전 (윈도우 인스톨러 및 macOS 설치 패키지 )
- 소스코드 빌드

## 버전과 에디션 ( 엔터프라이즈와 커뮤니티 ) 선택
---

> 💡 다른 제약사항이 없다면 가능한 한 최신 버전을 설치하는 것이 좋다.

기존 버전에서 새로운 메이저 버전으로 업그레이드 하는 경우라면 최소 패치버전이 15~20번 이상 릴리스된 버전을 선택하는것이 안정적이다. 
ex) 8.0.15 ~ 8.0.20

초기 버전의 MySQL 서버는 엔터프라이즈 에디션과 커뮤니티 에디션으로 나뉘어져 있었지만, 실제 기능에 차이가 있었던 것이 아니라 기술 지원의 차이만 있었다.
- MySQL 5.5 버전부터는 커뮤니티와 엔터프라이즈 에디션의 기능이 달라지면서 소스코드도 달라졌고, MySQL 엔터프라이즈 에디션의 소스코드가 더이상 공개되지 않는다.

<br/> 

> 💡 오픈 코어 모델 (Open Core Model) : 
핵심 내용은 엔터프라이즈 에디션과 커뮤니티 에디션 모두 동일하며, 특정 부가 기능들만 상용 버전인 엔터프라이즈 에디션에 포함되는 방식.

#### 엔터프라이즈 에디션에서만 지원하는 기능
- Thread Pool
- Enterprise Audit
- Enterprise TDE (Master Key 관리)
- Enterprise Firewall
- Enterprise Monitor
- Enterprise Backup
- MySQL 기술 지원

<br/>

## MySQL 설치
---

#### 1. 리눅스 서버의 Yum 인스톨러 설치하는 방법

Yum 인트솔러를 이용하려면 MySQL 소프트웨어 레포지토리를 등록해야 하는데 [MySQL Yum Repository](https://dev.mysql.com/downloads/repo/yum/) 에서 RPM 설치 파일을 직접 받아서 설치해야 한다.

> ##### Yum 레포지토리 설치용 RPM 다운로드

![](/assets/posts/03cff518c2a3bf65d04b43786720fecda7d1d888a6c7f06fc623b78b9a385f1f.png)


<br/>

```
Q : RPM이랑 YUM이 무엇인가요?
```
RPM, YUM은 리눅스의 패키지 인스톨 프로그램이라고 볼 수 있다. RPM은 필요한 패키지를 하나하나 수동으로 설치해야 하는 반면 YUM은 패키지를 받을 때 의존성을 찾아 자동으로 설치한다.

#### RPM ( Redhat Package Manager )
setup.exe와 비슷한 개념으로 프로그램 설치 후 바로 실행할 수 있는 패키지 설치 파일이다.
- 반드시 .rpm 파일이 있어야 한다.
- 의존성을 해결하지 못한다.
프로그램 B를 사용하기 위해 A가 필요하다면 수동으로 A를 미리 설치해야한다.

#### YUM ( Yellowdog Update Manager )
인터넷을 통해 필요한 파일을 저장소 (레포지토리) 에서 자동으로 모두 다운로드해서 설치하는 방식이다.

RPM과 달리 YUM은 패키지가 의존성을 필요로 하면 자동으로 의존성을 같이 설치한다.

<br/>

각 운영체제의 버전에 맞는 RPM 파일을 다운로드 해서 서버를 설치하고자 하는 리눅스 서버에서 다음과 같이 YUM 레포지토리 정보를 등록한다.
```shell
linux> sudo rpm -Uvh mysql84-community-release-el9-1.noarch.rpm
```
그러면 YUM 레포지토리 등록이 되고, MySQL 설치용 RPM 파일들이 저장된 경로를 가진 파일이 생성된다.
```linux
linux> ls -alh /etc/yum.repos.d/*mysql*
```

<br/>
인스톨러 명령을 이용해 버전별로 설치 가능한 MySQL 소프트웨어 목록 확인 가능하다.

```shell
linux> sudo yum search mysql-community # 어떤 RPM 패키지가 있는지 확인할 수 있다.

linux> sudo yum --showduplicates list mysql-community-server # 설치 가능한 모든 버전을 확인할 수 있다.
```

마지막으로 특정 버전을 설치하면 된다. 설치하고자 하는 버전을 패키지 이름 뒤에 `-` 로 구분하면 된다. 

```shell
linux> sudo yum install mysql-community-server-[특정 버전]

ex)    sudo yum install mysql-community-server-9.3.0
```

	Is this ok [y/d/N]:
이 프롬프트가 나오면 y를 눌러 나머지 설치를 진행하면 된다.

<br/>

리눅스 서버에서는 Yum 인스톨러나 RPM 설치를 하더라도 MySQL 서버를 바로 시작할 수 없다. 서비스용 MySQL 서버는 리눅스 서버에서 많이 사용하므로 리눅스 서버에의 설정 파일과 시스템 테이블 준비가 필요하다.

<br/>

### 리눅스 서버에서 Yum 인스톨러 없이 RPM 파일로 설치

Yum 인스톨러 없이 RPM 패키지로 직접 설치하려면 필요한 RPM 패키지를 수동으로 직접 다운로드해야 한다. 
**[MySQL RPM 다운로드 페이지](https://dev.mysql.com/downloads/mysql/)** 에서 운영체제의 버전과 CPU 아키텍처를 선택한 후, 다운로드 하면 된다. 원하는 버전이 따로 있다면 선택도 가능하다.

<br/>
RPM 패키지 다운로드 페이지에서 RPM 패키지 다운로드 한 후 의존 관계 순서대로 설치하면 된다. 

##### Development Libraries 패키지는 C/C++ 언어로 MySQL 서버에 접속하는 프로그램 개발/빌드시 필요한 파일들을 가지고 있어서 굳이 설치하지 않아도 된다.

의존 관계 순서는 아래 표와 같다. 위에서부터 아래 순서로 설치하면 된다.

##### RPM 패키지 목록

| RPM 패키지 | RPM 파일명 |
|---|---|
|Development Libraries|mysql-community-devel-9.3.0-1.el9.x86_64.rpm|
|Shared Libraries|mysql-community-libs-9.3.0-1.el9.x86_64.rpm|
|Compatibility|mysql-community-libs-compat-9.3.0-1.el9.x86_64.rpm|
|MySQL Configuration|mysql-community-common-9.3.0-1.el9.x86_64.rpm|
|MySQL Server|mysql-community-server-9.3.0-1.el9.x86_64.rpm|
|Client Utilities|mysql-community-client-9.3.0-1.el9.x86_64.rpm|

<br/>

### macOS용 DMG 패키지 설치

macOS에서 인스톨러로 설치하려면 설치에 필요한 DMG 패키지 파일을 직접 다운로드해야한다. DMG 파일을 실행하고 패키지 파일을 설치한다.
설치 옵션 변경창에서 설치위치는 기본 디렉토리로 유지하고, '설치' 버튼을 누른다.
라이선스 동의 화면에서는 2가지 사용자 인증 방식이 있다.

`Use Strong Password Encryption` 을 선택하면 Caching SHA-2 Authentication을 사용한다. _(권장)_
`Use Legacy Password Encryption` 을 선택하면 Native Authentication 방식을 사용한다.

MySQL 서버 설치시 기본 설정은 디렉토리와 로그 파일을 `/usr/local/mysql` 하위에 생성하고 **`관리자 모드`** 로 서버 프로세스를 기동하기 때문에 관리자 계정에 대한 비밀번호 설정이 필요하다.

<br/>

```shell
 kimhyeonjae@Ks-MacBook-Air  ~  ps -ef | grep mysqld
 
   74   554     1   0  4 425  ??        43:34.22 /usr/local/mysql/bin/mysqld --user=_mysql 
   --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data 
   --plugin-dir=/usr/local/mysql/lib/plugin --log-error=/usr/local/mysql/data/mysqld.local.err 
   --pid-file=/usr/local/mysql/data/mysqld.local.pid --keyring-file-data=/usr/local/mysql/keyring/keyring 
   --early-plugin-load=keyring_file=keyring_file.so
```
MySQL 서버가 설치된 디렉토리는 `/usr/local/mysql` 이고, 하위 각 디렉토리 정보는 위 결과와 같다.

이 디렉토리 중 중요 항목들을 살펴보자.

- `bin` : MySQL 서버와 클라이언트 프로그램, 유틸리티를 위한 디렉토리
- `data` : 로그 파일과 데이터 파일들이 저장되는 디렉토리
- `include` : C/C++ 헤더 파일들이 저장된 디렉토리
- `lib` : 라이브러리 파일들이 저장된 디렉토리
- `share`: 다양한 지원파일들이 저장돼 있으며, 에러 메시지나 샘플 설정 파일(my.cnf)가 있는 디렉토리

<br/><br/>

# 2.2 MySQL 서버의 시작과 종료
---

## 설정 파일 및 데이터 파일 준비
---

리눅스 서버에서 Yum 인스톨러나 RPM으로 MySQL 서버 설치하면 필요한 프로그램들과 디렉토리는 준비되지만, **트랜잭션 로그 파일과 시스템 테이블이 준비되지 않아서 서버를 시작할 수 없다.**

MySQL 서버를 실행하는데 필요한 초기 데이터 파일(시스템 테이블 저장되는 데이터 파일) 과 트랜잭션 로그(리두 로그) 파일을 생성하자.
초기에 `my.cnf` 에는 기본적인 설정만 존재 ( 윈도우는 `my.ini` )

<br/>

`--initialilze-insecure`  : 필요한 초기 데이터 파일과 로그 파일들 생성. **비밀번호가 없는 관리자 계정인 root 유저 생성**
```shell
linux> mysqld -- defaults-file=/etc/my.cnf --initialilze-insecure
```

`--initialilze` : 비밀번호를 가진 관리자 계정 생성하고자 할 때 사용. 생성된 관리자 계정의 비밀번호를 에러 로그 파일로 기록한다.
```shell
linux> mysqld -- defaults-file=/etc/my.cnf --initialilze
```
<br/>

## 시작과 종료
---

UNIX 계열에서는 자동으로 `usr/lib/systemd/system/mysqld.service` 파일이 생성된다.
`systemctl` 유틸리티를 이용해 MySQL을 기동하거나 종료하는 것이 가능하고, 상태도 확인 가능하다.

```shell
linux> systemctl start mysqld # 시작
linux> systemctl status mysqld # 서버 상태 확인
linux> systemctl stop mysqld # 종료
```
원격으로 서버를 셧다운 하려면 서버에 로그인 한 상태에서 SHUTDOWN 명령을 실행하면된다. 
이때 **SHUTDOWN 권한 (Privileges) 을 가지고 있어야 한다.**
```shell
linux> systemctl shutdown mysqld # 셧다운
```

<br/>

> #### `mysqld_safe`
`mysqld_safe` 스크립트를 이용해서 MySQL 서버 시작, 종료 가능하다.
MySQL 설정파일 (my.cnf) 의 "[mysqld_safe]" 섹션의 설정들을 참조해서 MySQL 서버를 시작하게 된다.
systemd 이용하는 경우에는 MySQL 설정파일의 "[mysqld_safe]" 섹션을 무시하게 된다.
>
> "[mysqld_safe]" 섹션에만 설정가능한 "malloc-lib" 같은 시스템 설정 적용가능하다.
#### malloc-lib?
- 메모리 할당 라이브러리 지정. 
- 장점 
	1. 서버 성능 향상 
    2. 메모리 단편화 감소 
    3. 메모리 누수 디버깅
- ex) 
  - jemalloc : 페이스북
  - tcmalloc (thread caching malloc) : 구글
>
> -> 멀티스레드 환경에 최적화 

<br/>

## 서버 연결 테스트
---

mysql을 실행하고 명령행 인자로 접속할 수 있다.
```shell
linux> mysql -uroot -p --host=@localhost --socket=/tmp/mysql.sock # (a) 소켓 파일로 접속
linux> mysql -uroot -p --host=127.0.0.1 --port=3306 # (b) TCP/IP 로 접속 (포트변호 명시)
linux> mysql -uroot -p # (c) 별도로 호스트 주소와 포트를 명시하지 않음
```

#### (a) 소켓 파일로 접속
`--host=@localhost` 옵션 사용하면 MySQL 클라이언트 프로그램은 항상 소켓 파일을 통해 MySQL 서버에 접속하게 되는데, 이는 'Unix domain socket' 을 이용하는 방식으로 TCP/IP를 통한 통신이 아닌 **유닉스의 프로세스 간 통신 (IPC; Inter Process Communication)의 일종이다.**

(b) TCP/IP 로 접속 (포트변호 명시)
하지만 `127.0.0.1` 을 사용하는 경우에는 자기 서버를 가리키는 루프백 (loopback) IP 이기는 하지만 TCP/IP 통신 방식을 사용하는 것이다.

(c) 별도로 호스트 주소와 포트를 명시하지 않음
이 경우 기본값으로 호스트는 `localhost` 가 되며 소켓 파일을 사용하게 된다.
소켓 파일의 위치는 설정 파일에서 읽어서 사용한다.

<br/>

MySQL 서버에 접속되면 MySQL 프롬프트가 **`mysql>`** 로 표시된다.
간단한 telnet 명령이나 nc (Netcat) 명령을 이용해 원격지 MySQL 서버가 응답 가능한 상태인지 확인 가능하다.
서버가 보내준 메시지를 출력한다면 네트워크 수준의 연결이 정상적임을 판단할 수 있다.

<br/><br/>

# 2.3 MySQL 서버 업그레이드
---

업그레이드 방법으로 2가지 방법이 있다.

#### 1. MySQL 서버의 데이터 파일을 그대로 두고 업그레이드하는 방법. (= In-Place Upgrade)
#### 2. mysqldump 도구 등을 이용해 MySQL 서버의 데이터를 SQL 문장이나 텍트스 파일로 덤프한 후, 새로 업그레이드 된 버전의 MySQL 서버에서 덤프된 데이터를 적재하는 방법. (= Logical Upgrade)

1번 방법은 여러 제약사항이 있지만 시간을 크게 단축할 수 있고, 반대로 2번 방법은 버전간 제약 사항이 거의 없지만 오래 걸릴 수 있다.

<br/>

### 인플레이스 업그레이드 제약 사항

1. **`메이저 버전 → 마이너 버전`** 간 업그레이드 
	- 대부분 데이터 파일의 변경 없이 진행된다. 
	- 여러 버전을 건너뛰는 것도 가능하다.
    - MySQL 서버 프로그램만 재설치하면 된다.

2. **`메이저 버전 → 메이저 버전`** 간 업그레이드 
	- 대부분 크고 작은 데이터 파일의 변경이 필요하기 때문에 반드시 직전 버전에서만 업그레이드 허용
    - 데이터 파일의 패치가 필요한데, 직전 메이저 버전에서 사용하던 데이터 파일과 로그 포맷만 인식하도록 구현되기 때문.
    - ex)  5.1 에서 8.0 으로 업그레이드 하려면 5.1, 5.6, 5.7, 8.0 으로 한 단계씩 업그레이드 해야한다.
    
3. 메이저 버전 업그레이드가 특정 마이너 버전에서만 가능한 경우도 있다.
   - 새로운 버전의 MySQL 서버를 선택할 때 최소 GA ( General Availability ) 버전은 지나서 15~20번의 마이너 버전을 선택하는 것이 좋다.
   
<br/>

### MySQL 8.0 업그레이드 시 고려 사항
- 사용자 인증 방식 변경 : MySQL 8.0 버전부터 Caching SHA-2 Autentication 인증 방식이 기본 인증 방식으로 바뀌었다.

- MySQL 8.0과의 호환성 체크 : 손상된 FRM 파일이나 호환되지 않는 데이터 타입 또는 함수가 있는지 mysqlcheck 유틸리티로 확인해볼 것을 권장한다.

- 외래키 이름의 길이 : 외래키 (Foreign Key) 의 이름이 64글자로 제한된다.

- 인덱스 힌트 : 인덱스 힌트가 있다면 MySQL 8.0에서 먼저 성능 테스트를 수행하자.
	
      Q 인덱스힌트란?
      
        : 특정 인덱스 사용하라고 강제하는 방법. 쿼리 성능 최적화.
          FORCE INDEX, USE INDEX, IGNORE INDEX

- GROUP BY에 사용된 정렬 옵션 : GROUP BY 절의 칼럼 뒤에 'ASC' 나 'DESC'를 사용하고 있다면 먼저 제거하거나 다른 방식으로 변경하자.

- 파티션을 위한 공용 테이블스페이스 : 파티션의 각 테이블스페이스를 공용 테이블스페이스에 저장할 수 없다.

<br/>

### MySQL 8.0 업그레이드

> 크게 두가지 단계로 나뉘어서 처리된다.

#### 1. 데이터 딕셔너리 업그레이드
데이터 딕셔너리 정보가 트랜잭션이 지원되는 InnoDB 테이블로 저장되도록 개선됐다.
데이터 딕셔너리 업그레이드는 기존의 FRM 파일의 내용을 InnoDB 시스템 테이블로 저장한다. MySQL 8.0 버전부터는 딕셔너리 데이터의 버전 간 호환성 관리를 위해 테이블이 생성될 때 사용된 MySQL 서버의 버전 정보도 함께 기록한다.

#### 2. 서버 업그레이드
시스템 데이터베이스 ( performance_schema 와 information_schema, 그리고 mysql 데이터베이스 ) 의 테이블 구조를 MySQL 8.0 버전에 맞게 변경한다.

<br/>

#### 8.0.15 버전 이전
"데이터 딕셔너리 업그레이드" 작업은 MySQL 서버 (mysqld) 프로그램이 실행,
"서버 업그레이드" 작업은 mysql_upgrade 프로그램이 실행했다.

예시)
1. MySQL 셧다운
2. MySQL 5.7 프로그램 삭제
3. MySQL 8.0 프로그램 설치
4. MySQL 8.0 서버(mysqld) 시작 (MySQL 서버가 데이터 딕셔너리 업그레이드를 자동 실행)
5. mysql_upgrade 프로그램 실행 (mysql_upgrade 프로그램이 시스템 테이블의 구조를 MySQL 8.0에 맞게 변경)

#### 8.0.16 버전 이후
1. MySQL 셧다운
2. MySQL 5.7 프로그램 삭제
3. MySQL 8.0 프로그램 설치
4. MySQL 8.0 서버(mysqld) 시작 (MySQL 서버가 데이터 딕셔너리 업그레이드를 실행 후, 시스템 테이블 구조를 MySQL 8.0에 맞게 변경)

즉, MySQL 서버가 업그레이드 됐다면 MySQL 서버 프로그램 (mysqld)이 시작되면서 자동으로 필요한 작업을 수행하기 때문에 사용자 실수를 더 줄일 수 있다.

> --upgrade 옵션을 이용해 데이터 딕셔너리 업그레이드를 수행할지 여부를 제어할 수 있다.

|파라미터 값 | 데이터 딕셔너리 업그레이드 | 서버 업그레이드|
|---|---|---|
|AUTO|필요한 경우 실행|필요한 경우 실행|
|NONE|X|X|
|MINIMAL|필요한 경우 실행|X|
|FORCE|필요한 경우 실행|항상 실행|

`MINIMAL` : "서버 업그레이드" 를 건너뛴다
`FORCE` : MySQL 서버의 시스템 테이블 구조가 잘못 변경됐거나 손상된 경우 사용
`AUTO` : 기본값. 필요한 경우 두 가지 업그레이드를 모두 자동으로 실행한다.

<br/><br/>

# 2.4 서버 설정
---

MySQL 서버는 단 하나의 설정 파일을 사용한다.
- 리눅스/유닉스 계열 : `my.cnf` 파일
- 윈도우 계열 : `my.ini` 파일

MySQL 서버는 시작될때만 이 설정 파일을 참조한다. 여러 개의 디렉토리를 순차적으로 탐색하면서 처음 발견된 my.cnf 파일을 사용한다.

다음과 같은 순서로 디렉토리를 탐색한다

1. `/etc/my.cnf` 파일
2. `/etc/mysql/my.cnf` 파일
3. `/usr/etc/my.cnf` 파일
4. `~/my.cnf` 파일

1번과 2번이 주로 탐색에 사용되고, 3번은 컴파일 시 내장된 경로다.
**즉, 단 하나의 설정파일만 사용하지만 설정 파일이 위치한 디렉토리는 여러 곳일 수 있다는 것이다.**

<br/>

### 설정 파일의 구성
하나의 설정 파일에 여러 개의 설정 그룹을 담을 수 있으며, 대체로 실행 프로그램 이름을 그룹명으로 사용한다. 

ex) [mysqld_safe] , [mysqld] , [mysql] , [mysqldump]

<br/>

### MySQL 시스템 변수의 특징
> MySQL 서버는 기동하면서 설정 파일의 내용을 읽어 메모리나 작동 방식을 초기화하고, 접속된 사용자를 제어하기 위해 이러한 값을 별도로 저장해둔다.
이렇게 저장된 값을 "시스템 변수 (System Variables)" 라고 한다.

각 변수가 시스템 변수인지 세션 변수인지 구분해야한다.

#### 시스템 변수가 가지는 속성
- **Cmd-Line** : MySQL 서버의 명령행 인자로 설정될 수 있는지 여부. "Yes" 이면 명령행 인자로 이 시스템 변수의 값을 변경 가능하다.
- **Option file** : 설정 파일인 my.cnf로 제어할 수 있는지 여부
- **System Var** : 시스템 변수인지 아닌지를 나타낸다. 언더스코어 형식으로 통일.
- **Var Scope** : 시스템 변수의 적용 범위를 나타낸다. 서버 전체 (**Global**) 를 대상으로 하는지, MySQL 서버와 클라이언트 간의 커넥션 (**Session**) 만인지 구분. 세션과 글로벌 모두 적용 (**Both**) 
- **Dynamic** : 시스템 변수가 동적인지 정적인지 구분하는 변수

<br/>

### 글로벌 변수와 세션 변수
> 글로벌 변수 : 서버 전체에 영향
> 세션 변수 : 현재 연결에만 영향 (연결 종료시 사라짐)

**`글로벌 범위의 시스템 변수`**는 **하나의 MySQL 서버 인스턴스에서 전체적으로 영향을 미치는 시스템 변수**다. _서버 자체에 관련된 설정_이다.

ex) MySQL 서버에서 단 하나만 존재하는 InnoDB 버퍼 풀 크기 (innodb_buffer_pool) 혹은 MyISAM의 키 캐시 크기 (key_buffer_size) 등

<br/>

**`세션 범위의 시스템 변수`**는 **MySQL 클라이언트가 서버 접속할 때 기본적으로 부여하는 옵션의 기본값을 제어하는 데 사용**된다.

ex) 각 클라이언트에서 쿼리 단위로 자동 커밋을 수행할 지 여부를 결정하는 autocommit 변수

<br/>

세션 범위의 시스템 변수 가운데 MySQL 서버의 설정파일에 명시해 초기화할 수 있는 변수는 대부분 범위가 "Both"다. 서버가 기억만 하고 있다가 실제 클라이언트와의 커넥션이 생성되는 순간에 해당 커넥션의 기본값으로 사용된다.
**설정파일에 초깃값을 명시할 수 없다.**

1. 글로벌 값은 서버가 기억만 한다. 
2. 새로운 연결(세션)시 이 글로벌 값이 복사된다.
3. 글로벌 값을 변경해도 기존 연결에 영향이 없다. (독립적)
4. 변경 후 만든 새 연결에서 새로 바꾼 글로벌 값이 복사되어 사용된다.

ex) 
	
    sort_buffer_size (256KB ~ 1MB)

	join_buffer_size (256KB)

	max_execution_time (0)  SELECT문 최대 실행시간 ms 단위로 제한
    
    
<br/>

### 정적 변수와 동적 변수
> 정적 변수 : 서버 실행중에 변경할 수 없다. 변경하려면 재시작 필요하다
> 동적 변수 : 서버 실행중에 변경 가능하다. SET 명령어로 즉시 적용된다.

MySQL 서버의 시스템 변수는 아래 2가지로 구분한다.

1. 디스크에 저장돼 있는 **설정 파일(my.cnf or my.ini)을 변경** 하는 경우
2. 이미 기동중인 MySQL 서버의 **메모리에 있는 MySQL 서버의 시스템 변수를 변경**하는 경우

<br/>

#### SET 명령어

- **`SET`** 명령어는 현재 실행중인 서버에만 적용된다. 
설정파일에 반영되는 것은 아니기 때문에 현재 기동중인 MySQL 인스턴스에서만 유효하다.
=> **my.cnf 파일은 바뀌지 않는다!** ( 재시작시 반영 안됨 )
<br/>

- **`SET PERSIST`** 로 현재 실행중인 서버에 변경을 적용한다.
또한 설정파일도 변경할 수 있다.
=> 이때 파일에 기록되는 것은 **`mysqld-auto.cnt` 라는 별도의 파일에 기록된다.** ( 재시작시 반영됨 )
<br/>

- **`SET PERSIST ONLY`** 는 현재 실행중인 서버에는 적용되지 않는다.
mysqld-auto.cnf 에만 수정해서 저장하고 재시작시 반영된다.

SET PERSIST 또는 SET PERSIST ONLY로 변경된 시스템 변수의 메타데이터는 `performance_schema.variables_info` 뷰 (모든 시스템 변수 메타데이터) 와 `performance_schema.persisted_variables` 테이블 (SET PERSIST로 저장한 시스템 변수 값) 을 통해 참조 가능핟.

두 테이블에서 일치하는 값 있는 행만 반환하기 위해 INNER JOIN 사용하면 된다.

**`RESET PERSIST`** : mysqld-auto.cnf 파일의 내용을 삭제해야 하는 경우에 사용한다.