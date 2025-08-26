---
title: "Operating System Concepts - (7)"
date: 2025-01-04T19:12:59.329Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-7"
image: "../assets/posts/4c7e460f3f00527879753eafcddc4214a208cc453220003693f71179b54f1d6b.png"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:56.368Z
  hash: "03434b398d6946d994bf7646a7d2a902505cefd83bb41e0978a301e22e68196a"
---

![](/assets/posts/4c7e460f3f00527879753eafcddc4214a208cc453220003693f71179b54f1d6b.png)

> # Memory Management - (2)

### Noncontiguous Allocation

- 주소 변환을 페이징 별로 해야하기 때문에 바인딩이 어려워짐.

1. **Paging** (페이징 기법)
   - Process의 Virtual Memory를 동일한 사이즈의 Page 단위로 나눔
   - Virtual Memory의 내용이 page 단위로 noncontiguous하게 저장됨.
   - 일부는 backing storage에, 일부는 physical memory에 저장됨.
   - Basic Method
     - Physical memory를 동일한 크기의 frame으로 나눔.
     - Logical memory를 동일 크기의 page로 나눔 (frame과 같은 크기)
     - 모든 가용 frame들을 관리
     - page table(각각의 페이지의 주소공간을 관리하는 배열)을 사용하여 logical address를 physical address로 변환
     - External fragmentation 발생 안함 (같은 크기로 분할하기 때문)
     - Internal Fragmentation 발생 가능 (마지막에 페이지 하나보다 남는 공간이 생길 수 있음. memory 공간이 페이지 개수만큼 할당되지는 않기 때문에.)![](/assets/posts/1f7efb322e8cb2bd87313949a86a6ce6d56c677a76b38bea659714bb201cf9ca.png)
   - 주소변환을 위해 paging table(배열)이 사용됨.
   - page가 들어갈 수 있는 공간을 paging frame이라고 함.![](/assets/posts/d9db94acd87aac65e84658a9d5e77cfafd759303f7a95f6937ac462b3f3466c3.png)
   - 앞부분이 논리적인 페이지 번호(p) / 뒤부분은 논리적인 주소(d)

### Implementation of Page Table

- Page table은 **main memory**에 상주
- **`Page-table base register(PTBR)`**가 **page table**을 가리킴
- **`Page-table length register(PTLR)`**가 테이블 크기를 보관
- 모든 메모리 접근 연산에는 **2번**의 **memory access** 필요
- **page table** 접근 1번, 실제 **data/instruction** 접근 1번
- 속도 향상을 위해 **associative register** or **translation look-aside buffer** **`(TLB)`** 라 불리는 고속의 lookup hardware cache 사용 (별도의 하드웨어를 사용하여 속도 향상을 도모함)

![](/assets/posts/deed4f1462f91daaf44f31f16307770bd822cd7c9839a9f4930ca789ae9c8d7c.png)
- 메모리 주소 변환을 위해 별도의 cache memory를 두는 것을 TLB라고 보면 됨.
- 페이지 테이블에 대한 일부 데이터를 caching함.
- TLB를 주소 변환 전에 먼저 검색하여 데이터를 체크하고, 존재한다면 바로 주소변환이 발생함.
- 정보 전체를 담고 있는 것이 아니라 **빈번이 참고되는 일부 데이터만을 담고 있기 때문에** 물리적/논리적 페이지의 쌍을 가지고 있음.
- TLB는 전체를 search해야 하기 때문에 **`Associative registers`**를 추가로 사용하여 병행 검색(parallel search)이 가능하도록 구현함.
- Address Translation (주소 변환)
  - page table 중 일부가 associative register에 보관되어 있음.
  - 만약 해당 page #(number)가 associative register에 있는 경우 곧바로 frame #를 얻음.
  - 그렇지 않은 경우 main memory에 있는 page table로부터 frame #를 얻음
  - TLB는 context switch 때 flush (remove old entries)

### Effective Access Time
![](/assets/posts/04e17bacde444d68d9bda8b9f86a447221060a0efb2d526d3a22f858b6b993f5.png)
- TLB로부터 주소변환이 되는 비율이 굉장히 높기 때문에 입실론(e)의 비율은 굉장이 적다.


### Two-Level Page Table
![](/assets/posts/826f6b8fd6f049d2e48fd566f795a39c993e0388f48aa12b596862c646f14d19.png)

- Outer-page table / Inner-page table(page of page table) 이렇게 두가지 테이블 사용
- 속도는 줄어들지 않지만, 메모리 사용량이 줄어들기 때문에 사용함.
- 현대의 컴퓨터는 **address space**가 매우 큰 프로그램을 지원함.
  - **32bit address** 사용 시 : **2^32 Byte (4GB)**의 주소 공간
    - **page size**가 **4K**일 시 **1M(백만)**개의 **Page table entry** 필요.
    - 각 **page entry**가 **4Byte**일 시 프로세스 당 **4M**의 **page table** 필요.
    - 그러나, 대부분의 프로그램은 **4G**의 주소 공간 중 지극히 일부분만을 사용하므로 **page table** 공간이 심하게 낭비됨.
  
  -> 따라서 **page table** 자체를 **page**로 구성하면 사용되지 않는 주소 공간에 대한 outer page table의 엔트리 값은 null (대응하는 inner page table이 없음, 그러므로 메모리 공간을 아낄 수 있음.)

- 페이지 테이블도 영역을 나누어 관리하는 것
- Logical address (on 32-bit machine with 4K page size)의 구성
  - 20 bit의 page number
  - 12 bit의 page offset
- page table 자체가 page로 구성되기 때문에 page number는 다음과 같이 나뉘게 된다.
  - 10-bit page number
  - 10-bit page offset
- 따라서, logical address는 page number (p1 - 10bit, p2 - 10bit)와 page offset (d - 12bit)로 구성되어 있게 된다.

  ![](/assets/posts/b180f79b9598404df942a8ff4af2b21197fb7ea4b9b5d4a7aceda4d307c27296.png)
 
- 여기서 p1은 outer page table의 index이고, p2는 outer page table의 page에서의 변위 (displacement)가 된다.

![](/assets/posts/5c7035d0673932615eec74bddfa21befeef8b9d079a2a4b0f3e5248730f7af88.png)

`d` : 페이지 하나의 크기가 4KB고, 4KB안에서 몇번째 byte냐를 나타내기 때문에 4K의 위치 부분이 필요. 4K는 2^12이므로 12Bit.
`p1, p2` : 각각 페이지테이블이 페이지화 되어 들어가기 때문에 4KB가 되고, 각각의 엔트리가 4Byte기 때문에 1K개(1024개)가 있다. 그렇기 때문에 이를 표현하기 위해 2^10인 10Bit.

### Multilevel Paging and Performance
- Address space가 더 커지면 다단계 페이지 테이블 필요
- 각 단계의 페이지 테이블이 메모리에 존재하므로 logical address의 physical address 변환에 더 많은 메모리 접근 필요
- TLB를 통해 메모리 접근 시간을 줄일 수 있음
- 4단계 페이지 테이블을 사용하는 경우
  - 메모리 접근 시간이 100ns, TLB 접근 시간이 20ns이고
  - TLB hit ratio가 98%인 경우
  effective memory access time = 0.98 x 120 + 0.02 x 520 = 128ns
  
  결과적으로 주소변환을 위해 28ns만 소요