---
title: "Operating System Concepts - (11)"
date: 2025-01-13T17:14:39.117Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-11"
thumbnail: "../assets/posts/63287f47fd47367e62b8c60dbbebf9686d92d0714ceaed364277f8063d1bc5b0.png"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:29.622Z
  hash: "33613ec5b4866284d87eaa15140ac671eb969310aac6e3b4101b7a093c8de14f"
---

> # File System (1)

### File and File System
- #### File
  - " A named collection of related information "
  - 일반적으로 비휘발성의 보조기억장치(e.g. hard disk) 에 저장
  - 운영체제는 다양한 저장장치를 file이라는 동일한 논리적 단위로 볼 수 있게 해줌.
  - Operation : create, read, write, reposition(lseek), delete, open, close 등
- #### File Attribute (or File metadata)
  - 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
    - 파일 이름, 유형, 저장된 위치, 파일 사이즈
    - 접근 권한 (읽기/쓰기/실행), 시간 (생성/변경/사용), 소유자 등
- #### File System
  - 운영체제에서 파일을 관리하는 부분
  - 파일 및 파일의 메타데이터, 디렉토리 정보 등을 관리
  - 파일의 저장 방법 결정
  - 파일 보호 등
  
  
### Directory and Logical Disk
- #### Directory
  - 파일의 메타데이터 중 일부를 보관하고 있는 일종의 특별한 파일
  - 그 디렉토리에 속한 파일 이름 및 파일 attribute들
  - Operation
    - Search for a file, create a file, delete a file
    - list a directory, rename a file, traverse the file system

- #### Partition (= Logical disk)
  - 하나의 (물리적) 디스크 안에 여러 파티션을 두는 것이 일반적
  - 여러 개의 물리적인 디스크를 하나의 파티션으로 구성하기도 함
  - (물리적) 디스크를 파티션으로 구성한 뒤 각각의 파티션에 **file system**을 깔거나 **swapping**등 다른 용도로 사용할 수 있음.
  
### open()
![](/assets/posts/44b100a84e5d5e27f086dab1ea984599eba0214c10727bbe00a9baeae767e9eb.png)

- open("/a/b/c")
  - 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 옴
  - 이를 위하여 directory path를 search
    - 루트 디렉토리 "/"를 open하고 그 안에서 파일 "a"의 위치 획득
    - 파일 "a"를 open한 후 read하여 그 안에서 파일 "b"의 위치 획득
    - 파일 "b"를 open한 후 read하여 그 안에서 파일 "c"의 위치 획득
    - 파일 "c"를 open한다.
  - Directory path의 search에 너무 많은 시간이 소요됨.
    - Open을 read/write와 별도로 두는 이유
    - 한번 open한 파일은 read/write시 directory search 불필요
  - Open file table
    - 현재 open된 파일들의 메타데이터 보관소 (in memory)
    - 디스크의 메타데이터보다 몇 가지 정보가 추가
      - Open한 프로세스의 수
      - File offset : 파일 어느 위치 접근 중인지 표시 (별도 테이블 필요)
  - File descriptor (file handle, file control block)
    - Open file table에 대한 위치 정보 (프로세스 별)

![](/assets/posts/83d4fc1f7c93f4489e096318ad65e21ca434fe28915b18f1513b9034d536ab1f.png)
1. 사용자 프로그램이 시스템 콜 ( /a/b 파일을 open하겠다 ) 
2. CPU 제어권이 OS로 넘어간다.
3. 이미 알고있던 root 디렉토리의 metadata를 메모리로 올린다. ( root를 먼저 open한다 )
4. root의 metadata에는 파일의 위치정보가 있으므로 이를 열어보면 root 디렉토리의 실제 내용이 어디있는지 위치를 찾을 수 있다.
5. root라는건 디렉토리 파일이기 때문에 이 디렉토리 하위 데이터의 metadata가 내용으로 들어가있다. 
6. a/b/였으므로 a의 메타데이터도 있으므로 a의 메타데이터도 메모리로 올린다. ( a를 open )
7. a의 metadata에는 a파일이 disk의 어디에 있는지 정보가 있고 찾아가서 내용을 열어보면 a라는 파일도 디렉토리 파일이다. a안에 b의 metadata도 있을것이다.
8. b도 반복하면 디스크의 b의 메타데이터도 찾아갈 수 있을것이다.
9. open이 끝나면 각 프로세스마다 그 프로세스가 open한 파일들에 대한 metadata포인터를 가지고 있는 배열에서 그 위치를 가리키는 포인터를 리턴한다. 이 리턴값을 file descriptor이라고 한다. 이 fd를 return한다.
10. 사용자 프로세스는 fd 값만 가지고 read write 등의 요청을 할 수 있다.

이때 read로 b의 내용을 읽어서 사용자 프로그램에게 전해주기 전에 먼저 운영체제가 자신의 메모리공간 일부분에 올려놓는다. 그 내용을 copy해서 사용자 프로그램에게 전달하는 것이다. 만약 어떤 프로그램이 동일한 파일의 동일한 위치를 요청하면 (Read System Call하면) Disk까지 가지 않고 운영체제가 **Buffer Chaching**을 통해 바로 전달해준다.

*이전 가상메모리 시스템에서는 페이징 기법에서 이미 메모리에 올라와 있는 페이지에 대해서는 운영체제가 중간에 끼어들지 못하고 하드웨어가 주소변환을 해서 바로 접근을 했다. Page Fault가 일어나면 CPU가 운영체제에게 넘어와서 운영체제가 Swap 영역에서 page를 읽어왔다. 이에 비해 file시스템에서는 이미 버퍼 캐싱 내용이 메모리에 있든 없든 System Call이기 때문에 CPU가 운영체제로 넘어온다. LRU, LFU 알고리즘을 자연스럽게 사용하게 된다. 페이징시스템에서 Clock 알고리즘을 썼던 것과 대조된다.

또한 Open file table에는 offset이라는 정보도 필요하다. 특정 프로세스가 특정 파일의 어디를 읽고있는지를 구분하기 위한 장치다. 그렇기 때문에 프로세스와 무관한 공통 정보인 파일의 metadata와 프로세스마다 개별로 file의 어디를 읽고있는지를 나타내는 offset이 필요하다. 

### File Protection
![](/assets/posts/bbb2f792c2a8b3101eaaa012f4eefec5e85c32a95d68c05a9e3610a506e0225c.png)
💡 File System의 Mounting
![](/assets/posts/9f69d1db4de7964bd3a46263065d044eabd3a25196a0fe9b5caaf025260f5409.png)![](/assets/posts/de10322691d94b50b66e5db9481170d90d95cf398144c51bf81bb95f30aaab9f.png)
- 마운트를 하게 되면 또 다른 파일 시스템의 root를 연결할 수 있음.
- 디스크에 또 다른 디스크를 연결하는 것.

### Access Methods
- 시스템이 제공하는 파일 정보의 접근 방식
  - 순차 접근 (sequential access)
    - 카세트 테이프를 사용하는 방식처럼 접근
    - 읽거나쓰면 offset은 자동적으로 증가
  - 직접 접근 (direct access, random access)
    - LP 레코드 판과 같이 접근하도록 함
    - 파일을 구성하 레코드를 임의의 순서로 접근할 수 있음.
