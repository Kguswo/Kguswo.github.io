---
title: "Operating System Concepts - (6)"
date: 2024-09-01T12:43:07.145Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-6"
image: "../assets/posts/d71169ee47d4be485a8bc4301f4a734562ab174197f5283fc131a7117dc5f9fe.png"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:02.945Z
  hash: "b93db3374206c3d8a5bb5387e27c9cabd109baf9aa0a6e2aefda1925558d93f3"
---

![](/assets/posts/15cea129be96dfbcafd04205a4c6f2be7e06be29d74b5b19af20d2fe0d67a84c.png)
> # Memory Management

##  Logical vs Physical Address
✔ **Logical Address** (= virtual address, 가상 주소)
  - 프로세스마다 독립적으로 가지는 주소 공간
  - 각 프로세스마다 0번지부터 시작
  - CPU가 보는 주소는 logical address
  
✔ **Physical Address**
  - 메모리에 실제 올라가는 위치
  - 물리적인 메모리 아래에는 OS가 올라가 있고, 그 위에 프로세스들이 올라가는 형태
  
## 주소 바인딩 (Address binding)
### 주소 바인딩
- 물리적인 메모리에 저장되는 위치, 즉 주소를 결정하는 것
- `Symbolic Address` --> `Logical Address` --> `Physical Address`
- Symbolic Address
  - 숫자보다는 Symbol로 되어 있는 Address를 사용하기 때문에 개발자가 보기 편하도록 사용하는 주소
  - Symbolic Address에서 Logical, Physical Address로 컴파일 되는 것.
  
  
### 주소 바인딩의 종류

1. Compile time binding
- 물리적 메모리 주소(physical address)가 컴파일 시 알려짐
- 시작 위치 변경시 재컴파일
- 컴파일러는 절대 코드(absolute code) 생성

2. Load time binding
- Loader의 책임하에 물리적 메모리 주소 부여
- 실행 시작시 비어있는 메모리 주소를 찾아 부여함
- 컴파일러가 재배치가능코드(relocatable code)를 생성한 경우 가능

3. Execution time binding (= Run time binding)
- 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음.
- 바인딩이 됐다가 실행 중에 프로세스의 메모리 위치가 변경되기도 하는 것.
- CPU가 주소를 참조할 때마다 binding을 점검 (address mapping table)
- 하드웨어적인 지원이 필요 (ex) base and limit registers, MMU
  - MMU가 메모리 위치를 변경(주소 변환)해주는 역할을 함.  
  
 ![](/assets/posts/8425d8184d3175121d2f06621d3295d7622e940731dcc728863f5dc38ae60278.png)
  - 현재는 Run time binding을 사용함.
  - CPU가 바라보는 주소는 Logical Address.
  
### Memory Management Unit (MMU)
- 주소 변환을 도와주는 하드웨어 장치
- Logical Address를 Physical Address로 매핑해 주는 Hardware Device
- MMU Scheme
  - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소값에 대해 base register (= relocation register)의 값을 더한다.![](/assets/posts/9b6ee1fb93f5c199ef01f89760cc50ddf83534d07ae37dd36727a0862a06e902.png)

  - relocation register, limit register 이렇게 두개의 레지스터로 주소 변환을 도와준다.
  - 논리 주소 + 시작 위치를 더해서 실제 위치를 파악함.
  - limit register는 프로그램의 크기를 담고 있는 레지스터. logical address가 범위 밖을 벗어나게 되는 경우 다른 프로그램의 위치를 요청하게 되기 때문에 이러한 악의적인 요청을 방어해야 함. 맨 처음 limit register가 범위 안에 존재하는 logical address인지 판별하고, 범위를 벗어나는 경우 트랩을 발생시켜 OS가 해당 요청을 파악하도록 함.
- user program
  - logical address만을 다룬다.
  - 실제 physical address를 볼 수 없으며 알 필요가 없다.
  - physical address는 MMU를 거쳐서 접근 가능함.
  
### Dynamic Loading
- 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때(동적으로, 필요할 때마다) 메모리에 Load 하는 것.
- memory utilization의 향상
- 가끔씩 사용되는 많은 양의 코드의 경우 유용 ex) 오류 처리 루틴
- 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능)
- 지금 시스템에서 OS가 페이징 기법을 통해 메모리를 관리하고 있지만, Dynamic loading은 프로그래머가 구현하는 영역. (페이징 기법과 Dynamic Loading은 다른 부분이 있지만, 같은 용어로 사용하기도 함.)
- Loading : 메모리로 올리는 것.

### Overlay
- 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림 (Dynamic Loading과 비슷)
- 프로세스의 크기가 메모리보다 클 때 유용
- 운영체제의 지원 없이 사용자에 의해 구현
- 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
  - _Manual Overlay_ (프로그래밍이 매우 복잡함. 어렵고 불편했음.)

### Swapping
- 프로세스를 일시적으로 메모리에서 **`backing store`**로 쫒아내는 것
- **Backing Store** (= Swap area)
  - 디스크 : 많은 사용자의 프로세스 이미지를 담을 만큼 충분히 빠르고 큰 저장 공간
- **Swap in / Swap out**
  - 일반적으로 중기 스케줄러 (swapper)에 의해 swap out 시킬 프로세스 선정
  - priority-based CPU scheduling algorithm
    - priority가 낮은 프로세스를 swapped out 시킴
    - priority가 높은 프로세스를 메모리에 올려 놓음
  - Compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in 해야 함.
  - Execution time binding(= Run time binding)에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음. 더 효율적임.
  - swap time은 대부분 transfer time (swap 되는 양에 비례하는 시간)임. transfer time은 원래 미미한 수준이지만 swap에서는 중요.![](/assets/posts/afc6e4ac42032a52db418f178fbacc9e28e9451e8d8b3d2a28ecf5fdc7c54d7b.png)

### Dynamic Linking
- Linking을 실행 시간 (execution time)까지 미루는 기법
- **Static Linking (<- static library)**
  - 라이브러리가 프로그램의 실행 파일 코드에 포함됨.
  - 실행 파일의 크기가 커짐
  - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비
    ex) printf 함수의 라이브러리 코드
   - static linking : 라이브러리 복제 (printf가 있는 프로세스 100개면 라이브러리도 100개 존재할 것)
   - dynamic linking : 라이브러리 링킹 (해당 주소만 복사, 라이브러리는 1개만 존재)
   
- **Dynamic Linking (<- shared library ex) .so, .dll)**
  - 라이브러리가 실행시 연결(Link)됨.
  - 실행 파일에는 라이브러리가 별도로 존재하고, 라이브러리 자체는 포함시키지 않고 Link만 두는 것.
  - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 `stub`이라는 작은 코드를 둠
  - 라이브러리가 이미 메모리에 있으면, 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
  - 운영체제의 도움이 필요.
 

### Allocation of Physical Memory
- 메모리는 일반적으로 두 영역으로 나뉘어 사용
  1. OS 상주 영역
     - interrupt vector와 함께 낮은 주소 영역 사용
  2. 사용자 프로세스 영역
     - 높은 주소 영역 사용![](/assets/posts/f89103b2695c5acd23a1540e18f77204b142d1f87b4507579d61a4a26db035c5.png)


- 사용자 프로세스 영역의 할당 방법
  1. Contiguous Allocation (연속 할당)
     - 각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것 (통째로 올라가는 것)
     - Fixed partition allocation
     - variable partition allocation
    
  2. Noncontiguous Allocation (불연속 할당)
     - 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음 (잘게 쪼개서 여러곳에 올라가는 것)
     - Paging
     - Segmentation
     - Paged Segmentation
     
### Contiguous Allocation
![](/assets/posts/f8a60407cb120013bcd966ac30899bbe5451f33f854537a387c3f54231de3b55.png)

1. ** Fixed partition allocation** (고정 분할 방식)
   - 물리적인 메모리를 몇 개의 영구적 분할 (partition)으로 나눔
   - 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
   - 분할 당 하나의 프로그램 적재
   - 융통성이 없음
     - 동시에 메모리에 Load되는 프로그램의 수가 고정됨.
     - 최대 수행 가능 프로그램 크기 제한
   - 외부 조각 (External Fragmentation)
     - 분할된 부분이 작아서 생기는 문제
   - 내부 조각 (Internal Fragmentation)
     - 분할된 부분이 커서 남는 부분이 발생해서 생기는 문제
  
2. **variable partition allocation** (가변 분할 방식)
   - 프로그램의 크기를 고려해 할당하는 방식
   - 분할의 크기, 개수가 동적으로 변형
   - 기술적 관리 기법 필요
   - 외부 조각 (External Fragmentation) 이 발생할 수 있음.

3. **Hole**
   - 가용 메모리 공간
   - 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있음.
   - 프로세스가 도착하면 수용 가능한 hole을 할당
   - 운영체제는 할당 공간(사용중인 공간), 가용 공간(hole) 정보를 유지함.![](/assets/posts/3a5bc81a82cecf7783c7cbc8f128faa5dc8fd78e2b4abc5add0c358d08c1ee48.png)

**Dynamic Storage Allocation Problem**
   - 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
   
 ✔ First-fit
   - size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
  
 ✔ Best-fit
   - Size가 n 이상인 가장 작은 hole을 찾아서 할당 (프로그램의 크기와 가장 비슷한 Hole 탐색)
   - Hole들의 리스트가 크기순으로 정렬되지 않은 경우 모든 hole의 리스트를 탐색해야 함.
   - 많은 수의 아주 작은 hole들이 생성됨. 3, Worst-fit
   - 가장 큰 hole에 할당.
   - 역시 모든 리스트를 탐색해야 함.
   - 상대적으로 아주 큰 hole들이 생성됨.
- First-fit과 Best-fit이 Worst-fit보다 속도와 공간 이용률 측면에서 더 효과적인 것으로 알려짐. (실험적인 결과, 약간 당연하기도 함)

4. **Compaction**
   - External Fragmentation(외부 조각) 문제를 해결하는 한 가지 방법
   - 사용 중인 메모리 영역을 한군데로 몰고 hole들을 다른 한 곳으로 몰아 큰 block을 만드는 것
   - 매우 비용이 많이 드는 방법임
   - 최소한의 메모리 이동으로 compaction하는 방법 (매우 복잡한 문제)
   - Compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행될 수 있다.
   
### Noncontiguous Allocation
주소 변환을 페이징 별로 해야하기 때문에 바인딩이 어려워짐.

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

