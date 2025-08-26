---
title: "Operating System Concepts - (8)"
date: 2025-01-08T15:07:19.149Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-8"
image: "../assets/posts/a486af1e5c3fee14c3d4d9ac4a03b4761c70ecdc39dc99589fcd57396cf9e6c4.png"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:04:43.415Z
  hash: "5ad2757d55584b451d722189ae8812dc4915207ca9a705cff23f31b9b04570cc"
---

![](/assets/posts/8508eb7574af38b56c9c79b704aae1f6081c22569b38496acc2594ae91c46145.png)

> # Memory Management - (3)

![](/assets/posts/97656e60b2be997715c199276b27c7c48454b33b2e3fd0d75bd52f41f2dcca0b.png)

- 맨 왼쪽 테이블이 Logical memory, Logical Memory의 개수만큼 가운데 있는 페이지 테이블에 엔트리가 존재한다.
- 논리적인 메모리가 최종적으로 물리적인 페이지 어디에 적재되어 있는지에 대해서도 페이지 테이블에 저장해둔다.
- 주소 변환 정보 뿐만 아니라 valid-invalid bit이 같이 들어있다.
- Logical Memory의 최대 개수만큼 페이지 테이블 엔트리가 존재하기 때문에, 현재 사용하고 있지 않는 영역이더라도 엔트리가 존재한다. 사진속 6, 7 페이지가 없지만 페이지 테이블은 존재하는 것과 같다. 이로 인해 valid-invalid bit을 통해 이미 물리적으로 적재된 페이지인지 판별이 가능하다. 
- 항상 메모리에 올라와있지 않거나, 물리적으로 적재되어있지 않은 경우 invalid bit을 표기하여 파악 가능하다.

### Memory Protection
- page table의 각 entry마다 아래의 bit를 둔다.
#### Protection bit
- page에 대한 접근 권한 (read/write/read-only)

#### Valid-Invaild bit
- "valid"는 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함 (접근 허용)
- "invalid"는 해당 주소의 frame에 유효한 내용이 없음을 뜻함. (접근 불허)
이 때, 유효한 내용이 없다는 의미는 프로세스가 **그 주소 부분을 사용하지 않고 있거나**,
**해당 페이지가 메모리에 올라와 있지 않고 swap area(backing store)에 임시 저장되어 있는 경우**를 뜻함.

### Inverted Page Table (역방향 페이지 테이블)
- page table이 매우 큰 이유
  - 모든 process 별로 그 logical address에 대응하는 모든 page에 대해 page table entry가 존재.
  - 대응하는 page가 메모리에 있든 아니든 간에 page table에는 entry로 존재
  - 공간 오버헤드가 큰 편.

- Inverted page table
  - page frame 하나 당 page table에 하나의 entry를 둔 것 (system-wide)
  - 각 page table entry는 각각의 물리적 메모리의 page frame이 담고 있는 내용 표시
(process-id, process의 logical address)
  - 단점 : 테이블 전체를 탐색해야 함 (시간적인 오버헤드 존재)
  - 해결 방법 : associative register 사용 (expensive) -> 별도의 하드웨어를 사용하여 시간적인 오버헤드를 줄이는 방법 

![](/assets/posts/31de6235b205d1f8ebcebbc794366d83c81c160974438d088d9dc58ef4054df8.png) 
- 실제 물리적 메모리의 프레임 개수만큼 테이블에 엔트리가 존재한다.
- 페이지 테이블의 첫번째 엔트리에는 물리적 메모리의 첫번째 프레임에 들어가는 논리적 메모리의 첫번째 프레임의 주소가 있고, 페이지 테이블의 두번째 엔트리에는 물리적 메모리의 두번째 프레임에 들어가는 논리적 메모리의 두번째 프레임 주소가 있다.
- 페이지 주소 변환이란, 논리적 메모리의 주소를 참조해서 물리적 메모리를 찾아가는 과정이지만, Inverted Page Table은 정반대 방향이다.
- 논리적 페이지의 페이지번호 p가 물리적 메모리의 몇번째 프레임에 위치했는지를 찾기위해서는, 페이지테이블은 모두 뒤져서 p가 위치한 엔트리를 찾은 뒤, 물리적 메모리의 어디에 위치했는지를 알 수 있다.
목적과는 맞지 않지만 페이지 테이블이 메모리에서 차지하는 공간을 줄이고자 이를 사용한다. 하지만 시간적인 오버헤드가 존재하고 p가 어느 프로세스의 p인지 파악하기 위해 프로세스를 구분하는 pid도 저장해야한다.

1. CPU가 logical address를 준다.
2. 1번에서 받은 논리적 주소를 가지고 page entry를 뒤져서 p가 위치하는 물리적 메모리의 frame 위치를 파악한다.
3. p가 여러개 존재할 수 있기 때문에 현재 CPU를 점유하고 있는 프로세스의 PID를 동시에 줘서 그 PID를 가진 P가 물리적 메모리의 어떤 위치에 존재하는지 찾는다.
4. 찾은 위치가 page table에서 몇번째 떨어진 위치인지(여기서는 f만큼 떨어진 엔트리) 파악해서 물리적 메모리에 가서 f번째 떨어진 엔트리를 찾음으로써 주소 변환을 완료한다.

### Shared Page
![](/assets/posts/4e2e4d514dc4c49be7aa8a1d365b10a0bcad0a8c43287a29957736f5d14f5d01.png)

- **Shared code** (**Re-entrant Code**, 재진입 가능 코드, **Pure code**)
  - read-only로 하여 프로세스 간에 하나의 code만 메모리에 올림 (코드 공유).
e.g. text editors, compilers, window systems
  - shared code는 모든 프로세스의 **_logical address space_**에서 동일한 위치에 있어야 함.
  - 공유할 수 있는 코드를 하나만 올려서 여러 프로세스와 매핑. Read-only로만 세팅해서 물리적 메모리에 하나의 code만 올림.
  
- **Private code and data**
  - 각 프로세스들은 독자적으로 메모리에 올림
  - Private data는 logical address space의 아무 곳에 와도 무방
  - 만약 하나의 워드 프로그램을 3개 사용한다고 하면, 같은 프로그램을 사용하기 때문에 프로그램 코드는 share가 가능함. 이렇게 share가 가능한 코드들에 대해서는 물리적 메모리에 하나의 코드만 올려서 이걸 공유함. 각각의 프로세스마다 다른 데이터 코드만 메모리에 별도로 올려서 사용.
  
> ## 2. Segmentaion

### Segmentation (세그먼테이션 기법)![](/assets/posts/f1e9d43d2cd1388e457d142243bf63a3a2a3f28bf7101e7100ec2ff102514cfb.png)

- 의미 있는 크기, 공간별로 자름. 크기가 균일하지 않음.
  - 작게는 프로세스를 구성하는 주소 공간(함수) 하나하나를 의미 단위로 잘라 세그먼트로 정의
  - 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능.
- 코드 세그먼트 / 데이터 세그먼트 / 스택 세그먼트 등으로 자름. 함수 별로도 자를 수 있음.
- logical unit 예제
  - main(), function, global variables, stack, symbol table, arrays
  
### Segmentation Architecture
- Logical address는 다음의 두 가지로 구성됨. 
**< Segment-number, offset >**
- **Segment table** (주소변환이 필요하므로 테이블 존재)
  - each table entry has
       - **base** (starting physical address of segment, 물리 주소),
       - **limit** (length of the segment, 세그먼트의 개수)
- **STBR** (Segment-table base register)
  - 물리적 메모리에서의 segment table의 위치
  - table이 어느 위치에 저장되어 있는지
- **STLR** (Segent-table length register)
  - 프로그램이 사용하는 segment의 수
segment number s가 STLR보다 작으면 오류 발생 !!

![](/assets/posts/91b065978a48fcb08b39d20ac0c72bc9e941dffa57d05bf2cb39d07cdece46dc.png)
- 프레임의 크기가 일괄적인 페이징 기법과 달리, 세그먼트 기법은 unit별로 크기가 다르기 때문에 물리적 메모리에서 세그먼트가 시작하는 위치인 base와 함께 세그먼트의 크기인 limit 값도 가지고 있음.
- 페이징에서는 프레임 크기가 모두 동일하기 때문에 프레임 번호만으로도 주소를 찾아갈 수 있음
- 주소 변환 시 확인해야할 것은 세그먼트 번호가 STLR보다 작아야 하고, 세그먼트의 크기 limit보다,
세그먼트가 위치하는 곳을 나타내는 offset의 크기 d의 값이 더 크면 역시 scope error이기 때문에 오류 발생.
- 이러한 오류가 발생하지 않을 때 비로소 세그먼트가 시작하는 위치 base에서 offset의 크기 d를 더한 주소로 이동해 주소 변환을 진행함.

1. ***Protection***
- 각 세그먼트 별로 protection bit가 있음
- Each entry
  - `Vaild bit = 0` => illegal segment
  - `Read/Write/Execution` 권한 bit
2. ***Sharing***
  - **shared segment** (같은 논리 주소 보유)
  - **same segment number**
  - 장점 : segment는 의미 단위이기 때문에 공유와 보안에 있어서 paging보다 훨씬 효과적이다.
3. ***Allocation***
- first fit / best fit
- external fragmentation 발생
- 단점 : segment의 길이가 동일하지 않으므로 가변 분할 방식에서와 동일한 문제점들이 발생함.

> ### 정리

- 단점 : segmentation의 크기가 동일하지 않기 때문에 hole 문제가 발생 가능
- 장점 : 의미단위로 일을 처리할 때에는 매우 효과적, 의미 단위로 세그먼트를 나누기 때문에 Read/ Write 등등 구분하는 게 자연스러움

![](/assets/posts/609162a9b0837197fb9129439d806431ccfac644423e02ed34cc92fbd5fdc0c2.png)
- 페이지는 개수가 정말 많지만, 세그먼트는 운영해보면 개수가 몇개 안됨. (이론적인 비교는 앞쪽, 실질적인 비교는 이러함)
- 그러므로 테이블에 의한 메모리 낭비는 페이징이 더 심함. 세그먼트는 좀 더 적음.
- 사진은 5개의 세그먼트로 형성된 경우, 0번 세그먼트의 시작 위치 (base)와 크기(limit)가 표로 형성되어 있음.
- 세그먼트 별로 물리 메모리에 적재되어 있을 수도 있고, 아닐 수도 있음.

### Sharing of Segments
![](/assets/posts/75546df58279625cebf1831cd29a41fffa69bfc31875a775f6a7424ca231fe4b.png)
- 같은 역할을 하기 때문에 공유를 하는 것
- 세그먼트 번호가 같고, 물리적인 세그먼트 위치가 같아야 함.
- 주소 변환 시 같은 주소로 변환됨. private segment의 경우 다른 위치에 적재되어 있음.

### Segmentation with paging
![](/assets/posts/dc298f605923fcd05fc9fb10f2f4102fccc8f9ec195476cac9a29456d2f83d41.png)
- segment 하나가 여러 개의 page로 구성. 때문에 메모리에 올라갈 때 page 단위로 잘려서 올라감.
**segment table entry**가 물리적 메모리에서 segment가 어디서 시작하는지 나타내는 **base address가 아니라** segment를 구성하는 page의 위치를 나타내는 **page table의 base address**를 가지고 있음.
**=>** 이를 통해 segment의 크기가 각각 달라 hole이 발생하는 문제를 해결.
- 어떤 segment를 read-only로 설정할 것인지 등등의 의미단위 작업은 segment table에서 미리 설정.

1. logical address에서 s를 통해 setment table 내의 몇 번째 segment entry를 나타내는지 확인
2. 그렇게 알게 된 segment table의 s에서 segment 내부의 page의 시작 위치인 page-table base를 확인
3. 한편, 물리적 메모리에서 해당 segment가 어디에서 시작하는지 나타내는 offset값 d와 s를 비교하여 error를 검출
4. 3번에서 이상이 없다면 offset값 d를 나눠서 page의 page-table에서 몇 번째 page entry인지 나타내는 p값과 page의 어디에서 시작되는지 나타내는 offset 값 d'를 확인
5. 1~4번에서 얻은 값을 통해 최종적으로 논리적 주소를 물리적 메모리 주소로 변환

- 이 모든 작업은 MMU라는 하드웨어와 CPU가 해줘야 하는 일.
여기서 운영체제가 하는 일은 **없음**. 운영체제는 IO Device에 접근할 때. 하지만 메모리 접근 시에는 하드웨어가 작업.



