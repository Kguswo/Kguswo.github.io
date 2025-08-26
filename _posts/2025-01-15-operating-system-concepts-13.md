---
title: "Operating System Concepts - (13)"
description: "Logical block디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들주소를 가진 1차원 배열처럼 취급정보를 전송하는 최소 단위SectorLogical block이 물리적인 디스크에 매핑된 위치Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터이다."
date: 2025-01-14T17:33:32.818Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-13"
thumbnail: "/assets/posts/43a098d41236e9b63cc0e830e9f995e2ff6dae0cc1fb7807bb71d48949680310.png"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:24.153Z
  hash: "f0f933eebae1b22f50a792ef90818e7d1dd0058b8241c84bd618b637cbcf94e8"
---

> # Disk Management and Scheduling

### Disk Structure
- Logical block
  - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
  - 주소를 가진 1차원 배열처럼 취급
  - 정보를 전송하는 최소 단위
- Sector
  - Logical block이 물리적인 디스크에 매핑된 위치
  - Sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터이다.
  - Logical Block과 Sector는 매핑되어 있음. 0번 섹터는 무조건 부팅과 관련된 데이터 저장.
 

### Disk Management
- Physical Formatting (low-level formatting)
  - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
  - 각 섹터는 header + data(512bytes) + trailer로 구성
  - header와 trailer는 sector number, ECC(Error-Correcting Code) 등의 정보가 저장되며 controller가 직접 접근 및 운영.
- Partitioning
  - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
  - OS는 이것을 독립적인 disk로 취급함 (logical disk)
- Logical Formatting
  - 파일 시스템을 만드는 것
  - FAT, inode, free space등의 구조 포함
- Booting
  - ROM에 있는 "small bootstrap loader"의 실행 (CPU의 instruction 형태로 실행됨.)
  - sector 0(boot block)을 memory에 load하여 실행
  - sector 0은 "full Bootstrap loader program"
  - OS를 디스크에서 load하여 실행
 
### Disk Scheduling
![](/assets/posts/6518caa068419e392b2621cd9d745226f80c8e4ec40c677a388d300bf9b0f674.png)
- Access time의 구성
  - Seek time
    - 같은 트랙에 위치하는 것을 실린더라고 하는데, 해당 실린더로 헤드를 움직이는 데 걸리는 시간을 의미한다.
  - Rotational latency
    - seek time보다 1/10정도의 적은 시간 소요
  - Transfer time
    - 실제 데이터의 전송 시간. 매우 작은 시간 소요
- Disk bandwidth
  - 단위 시간 당 전송된 바이트의 수
- Disk Scheduling
  - seek time을 최소화하는 것이 목표 (디스크 접근 시간이 거의 seek time에 따라 좌우되기 때문에)
  - seek time = seek distance
  
  
### Disk Scheduling Algorithm
큐에 다음과 같은 실린더 위치의 요청이 존재하는 경우 디스크 헤드 53번에서 시작한 각 알고리즘의 수행 결과는? (실린더 위치는 0-199) 98, 183, 37, 122, 14, 124, 65, 67

#### 1. FCFS
![](/assets/posts/f27c15a16ede8d58b53df254e0b671970486892af1548fec1f3d7100f7756b06.png)
- 처음부터 순서대로 진행
- 디스크가 상당히 많이 이동하는 것을 볼 수 있음.
- 비효율적

#### 2. SSTF (Shortest Seek Time First)
- head의 위치에서 가장 가까운 요청부터 처리. (seek time이 가장 적기 때문에)
- 디스크 헤드의 이동거리는 줄어들지만 starvation 문제 발생 가능성 높음.

#### 3. SCAN (= elevator scheduling)

![](/assets/posts/c6c4aa579918decb076336baaddfb81ccdf39819890c6aa291e90d50c6dbbfe0.png)![](/assets/posts/e56904dd0b258f3f327d2c83781f3ec618f95499f55588fe5fcbb06d1ca9bb80.png)
- disk arm이 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 골목에 있는 모든 요청을 처리한다.
- 다른 한쪽의 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동한다.
- 문제점 : 실린더 위치에 따라 대기 시간이 다르다.
- 엘레베이터 작동 원리에 비유함.

#### 4. C-SCAN (circular scan)
- 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
- 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
- SCAN보다 균일한 대기 시간을 제공한다.
#### 5. N-SCAN
- SCAN의 변형 알고리즘
- 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 service

#### 6. LOOK, C-LOOK
- SCAN이나 C-SCAN은 헤드가 디스크 끝에서 끝으로 이동
- LOOK과 C-LOOK은 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 이동한다.
![](/assets/posts/2ac03799e7f8bb2c2472c8f17e88f591011fbd16a62bbf11936acd78c2221209.png)

### Disk Scheduling Algorithm의 결정
- SCAN, C-SCAN 및 그 응용 알고리즘은 LOOK, C-LOOK 등이 일반적으로 디스크 입출력이 많은 시스템에서 효율적인 것으로 알려져 있음.
- file의 할당 방법에 따라 디스크 요청이 영향을 받음.
- 디스크 스케쥴링 알고리즘은 필요할 경우 다른 알고리즘으로 쉽게 교체할 수 있도록 OS와 별도의 모듈로 작성되는 것이 바람직하다.

### Swap-Space Management
![](/assets/posts/13ac6b7915b5f46da79c17e987a84027f444d58e8d9d24a3a585ad8c4f45a89b.png)
- 메모리가 휘발성인 특성을 가지고 있기 때문에 비휘발성인 디스크를 사용하는 것
- 메모리의 공간이 부족하기 때문에 메모리의 연장 공간으로 SWAP Space를 할당하여 사용함.
- Swap-space
  - 물리적인 디스크를 파티셔닝하여 논리적인 디스크를 만든 후 그 논리적인 디스크 하나를 swap으로 할당하는 것.
  - 보통 큰 단위의 데이터를 올리고 내리면서 짧은 시간에 빠르게 서비스를 사용함. (512K)
 
 
### RAID
- RAID (Redundant Array of Independent Disks)
  - 여러 개의 디스크를 묶어서 사용
- RAID의 사용 목적
  - **디스크 처리 속도 향상**
    - 여러 디스크에 block의 내용을 분산 저장
    - 병렬적으로 읽어 옴 (interleaving, striping)
  - **신뢰성 (reliability) 향상**
    - 동일 정보를 여러 디스크에 중복 저장
    - 하나의 디스크가 고장(failure) 시 다른 디스크에서 읽어옴(Mirroring, shadowing)
    - 단순한 중복 저장이 아니라 일부 디스크에 parity를 저장하여 공간의 효율성을 높일 수 있다.
    
### UNIX 파일 시스템
![](/assets/posts/ae6a6058a902bd28377351d806c322f61f31d180c4fdc4bda53421adc6f02806.png)![](/assets/posts/38a204f6c021832dc892abda4aea365bb6bb5ac28531f7f3e78b379f18e690df.png)
- inode에는 메타데이터 정보를 보관하고 있다.
![](/assets/posts/47bdfc4a869af84ce292ebf32cea760d96310a6e216105a75f81e5520b49f38b.png)
- 현재 가장 많이 사용되는 파일시스템은 EXT4이다.

### 현재 가장 많이 사용되는 파일시스템은 EXT4이다.
![](/assets/posts/f25725179f43a365a97726b9781b77fb71a98b3461780a942a655cbcb29d084b.png)
- indirect pointer는 큰 파일을 지원하기 위해서이다.

![](/assets/posts/e1b3c85f2b218cf57663a8524bbd3e009e13d437798e9bbed501328d4ed0da5f.png)
- 원래 파일에 접근하기 위해서는 메타데이터에 접근하고, 실제 파일에 접근하는데 i-node block과 data block을 왔다갔다하는 것은 디스크 헤더가 많이 움직인다. 이러한 단점을 보완하기 위해서 블록의 그룹화가 나왔다.

![](/assets/posts/0bdba018fd74b60f97e36ded6908de20cc0e14284b769530e0bb8dbf0d9f9124.png)
- 그룹에 대한 총체적인 정보를 담고 있는 것이 Group descriptor이다.
- 사용적인 블록과 비어있는 그룹은 block bitmap으로 구분한다.
![](/assets/posts/e3e415630203617e8cfc64cd5048d5d954c488fd0f017b5742dbcb72903b7ea8.png)

### Ext4 파일시스템
![](/assets/posts/46ad4ad971221961d85f76b0acbf30f836351b0f6e4133185fb3c8a6b5f8205c.png)![](/assets/posts/bb11e9b861ee088d31d76ae46d25267b3255142bbee3acb97ccff14c32b75f6b.png)![](/assets/posts/ca3e6fc10396c22f9181fc158dffa6cf58d5aa35ee11bc07d50427e1823fe18f.png)

### What happened?
![](/assets/posts/dc9a154c801b335ba741c1b758a1ca1151c1e7e49c4ac0d53f4732f5f10a9be6.png)![](/assets/posts/12b911995a1ac16e2a71675f0090a4e0eb3a282278e8f469ac8218e24233833f.png)
- 버퍼캐시는 휘발성
- 전원이 나가서 메모리의 내용이 휘발되면 파일시스템의 내용이 깨질 수 있다.

### How to solve this problem?
![](/assets/posts/55ca2df9507226f78a322e272c0e15a6baca54b3b5599f72045b0206a7aa27fb.png)![](/assets/posts/0d3fce37aa3644003cc0fd50bdb66f3fd7b6f9410dde2d11f45eeda54f016b77.png)
- 버퍼캐시에서 쫓겨날때 파일에 저장을 해주는 것이 아니라, 주기적으로 스토리지에 써주는 것이다. 파일시스템에 쓰는것이 아니라 저널 영역이라는 것에 써주는 것이다. 도중에 크래쉬가 나더라도 저널영역에 있는 것을 옮겨쓰면 된다.
![](/assets/posts/e9bd1ab75711d4a9136ef330c5c315aa34ea553cc799128cdb2efe7a8170042b.png)

### Ext4의 저널링 (메타데이터 저널링 모드)
![](/assets/posts/b8f989f252fc6d6ecc550dadb50559a9c7bc4b0b8284d48b48f9838288344e16.png)![](/assets/posts/8edd364bcbea64304fdc1e6b191b38d9a96c59875dba3bbc423db22804bae280.png)
- 이건 메타데이터만 저널링하는 방법이다. 메타데이터만 저널링했기 때문에 파일의 위치정보는 깨지지않는다. 일반 데이터는 깨질 수 있다.

### Ext4의 저널링 (데이터 저널링 모드)
![](/assets/posts/b27f4b6224e4dd4669f0f52270fe4cd33ec617bea11bd1e3fa6fd3848d56eda6.png)

### 파일시스템을 위한 버퍼캐시 알고리즘
![](/assets/posts/d977fdeb2fc17af95b4100ab53e91281f72321a915460f80dabbc88445a12456.png)
- LRU와 LFU 둘다 일부분의 정보만 사용하기 때문에 단점이 있다.

### LRFU 알고리즘
![](/assets/posts/5b10793b80b012720b9d6888a9f5a0fe4e205dad7b2c518be154e0ad1a19fcfc.png)

### LRFU 알고리즘의 실효성
![](/assets/posts/588c99632e582fd723cd956f2af2c573579b537c4e6058fff0ebd7c3727c685f.png)
- 어떤 것을 쫓아낼지 logn 안에 결정 할 수 있어야하는데 이렇게하면 O(n)이다.

### LRFU의 효율적인 구현 방법
![](/assets/posts/636436ea38f56218ef1524d350d229dde1a2fbfe511a5a1bf0930af483a1d277.png)![](/assets/posts/dc39d38da091e18900cafaf0989690678e9bc1e36aeb54b810c81ee4765f111b.png)![](/assets/posts/f61d454949c339da09f2e582e43627cb2b4132c00388b7a8574840a14c42bfdd.png)![](/assets/posts/43f76cbb4e111d120ac68197324677be8f81cfc2d33f6288c00997669eb27db2.png)




