---
title: "Operating System Concepts - (2)"
date: 2024-07-14T14:37:40.605Z
tags: ["공룡책","운영체제"]
slug: "Operating-System-Concepts-2"
image: "../assets/posts/4c294851c32159891945b7e1f81361f1d13a45604e61114fc15b2c105d02398c.PNG"
categories: 운영체제
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:06:27.938Z
  hash: "d93a9ae38f73a7d1e997a5321442eb4666e235839dd6b3e878c726ef9e198314"
---

> ### 컴퓨터 시스템의 구조 (Ch2)

- 하드 디스크는 input device/output device 둘 다의 역할을 한다.
  - cpu안에는 memory보다 빠른 저장 공간이 있음. 이를 register라고 함.
- Interrupt line은 항상 프로그램이 memory 영역만을 사용해서 작동하기는 어렵기 때문에, I/O device 접근을 위해 프로그램 실행 중 interrupt를 걸어 해당 device에서 데이터를 읽거나 쓴다.


> ### Mode Bit (p.26 참조)

![Mode Bit](/assets/posts/892a46133d7306bff44b8e844353371016705359d72be11cb1183e9253332287.png)

- 사용자 프로그램의 잘못된 수행으로 다른 프로그램 및 운영체제에 피해가 가지 않도록 하기 위한 보호 장치 필요.
- cpu는 항상 interrupt line을 체크해 프로그램 실행 중 interrupt가 발생했는지를 확인함.
- 트랩이나 인터럽트가 발생할 때마다, 하드웨어는 사용자 모드에서 -> 커널모드로 전환.(즉 mode bit를 0으로 변경)
- Mode bit을 통해 하드웨어적으로 두 가지 모드의 operation 지원

`1 (User mode)` : 사용자 프로그램 수행
운영체제가 CPU에서 실행중. 모든지 실행을 할 수 있음.
`0 (Monitor mode, kernel mode, system mode)` : OS Code 수행

- 보안을 해칠 수 있는 중요한 명령어는 모니터 모드에서만 수행 가능한 "특권 명령"으로 규정
- Interrupt나 Exception 발생시 하드웨어가 mode bit을 0으로 바꿈
- 사용자 프로그램에게 CPU넘기기 전 mode bit 1로 세팅

> ### Timer (p.28 참조)

- **타이머** : 사용자 프로그램이 무한루프(Infinite loop)에 빠지거나 시스템 서비스 호출에 실패하여, 제어가 운영체제로 복귀하지 않는 경우가 없도록 방지해야한다.
  - 정해진 시간이 흐른 뒤(초기에 정해진 시간을 할당함) 운영체제에게 제어권이 넘어가도록 인터럽트 하도록 설정할 수 있다.
  - Timer는 특정 프로그램이 cpu를 독점하는 것을 막기 위한 역할을 수행함. Timer에 값을 세팅한 후 프로그램을 cpu에 전달함. 세팅한 시간이 지나면 interrupt를 걸어 프로그램이 멈추도록 함.
  - 타이머는 매 클럭 틱 때마다 1씩 감소
  - 타이머 값이 0이 되면 타이머 인터럽트 발생
  - CPU를 특정 프로그램이 독점하는 것으로부터 보호
- 타이머는 time sharing을 구현하기 위해 널리 이용됨.
- 타이머는 현재 시간을 계산하기 위해서도 사용됨.

> ### Interrupt (인터럽트) (p.540 참조) 

**Interrupt(인터럽트)**
컨트롤러가 장치 드라이버에게 작업을 사실을 어떻게 알릴까? 인터럽트를 통해서!

- 하드웨어는 어느 순간이든 시스템 버스를 통해 CPU에 신호를 보내 인터럽트를 발생시킬 수 있다. 인터넙트는 운영체제와 하드웨어의 상호 작용 방식의 핵심 부분이다.
- 즉 컴퓨터에서 신호를 보내 이벤트 발생을 알리는 것을 의미한다.
- 보통 컴퓨터에서 여러 작업을 동시에 처리하는데, 기존 작업을 중단하고 타 작업을 진행해야 할 때 인터럽트 신호를 보내 기존작업을 중단하라고 신호를 보낸다. 그러면 커널이 작업을 멈추고 인터럽트 처리한 뒤 기존 작업으로 돌아온다.
![Interrupt](/assets/posts/8324d9d66cf19b7529adfe281209823c2788773e626a184b4b38172257fb85de.png)

- Trap(트랩) : 소프트웨어에 의해 발생하는 인터럽트
- 하드웨어는 System Bus(시스템 버스) 를 통해 CPU에 신호 보내서 인터럽트 발생시키고, 소프트웨어는 System Call(시스템 콜) 이라는 명령으로 인터럽트 발생시킨다.

- 인터럽트 당한 시점의 레지스터와 program counter를 save 한 후 CPU의 제어를 인터럽트 처리 루틴에 넘긴다.
- **Interrupt**(넓은 의미)
  - `interrupt` (하드웨어 인터럽트) : 하드웨어가 발생시킨 인터럽트. 일반적인 의미의 interrupt.
  - `trap` (소프트웨어 인터럽트)
    - `Exception` : 프로그램이 오류를 범한 경우
    - `System Call` : 프로그램이 커널 함수를 호출하는 경우

- 인터럽트 관련 용어
  - `인터럽트 벡터`
    - 해당 인터럽트의 처리 루틴 주소를 가지고 있음.
  - `인터럽트 처리 루틴` ( = Interrupt Service Routine, `인터럽트 핸들러`)
    - 해당 인터럽트를 처리하는 커널 함수

> ### System Call(시스템 콜) (p.24, 26 참조) 

#### 시스템 콜 
- 사용자 프로그램이 운영체제의 서비스를 받기 위해 커널 함수를 호출하는 것
![시스템 콜](/assets/posts/b293794743d2cd702ee185744b901830884fc7cacd663fb4b8a982f418e51258.png)


- **Trap** : 하드웨어 인터럽트와 다르게 Trap(트랩, 또는 오류)가 있는데, 이는 Exception(ex: 0으로 나누거나 유효하지 않은 메모리 엑세스) 또는 사용자 프로그램의 특정 요청 때문에 발생하는 소프트웨어 생성 인터럽트이다. 
- 이 특정 요청은 **시스템 콜**이라는 특수 연산을 실행하여 요청되고 운영체제가 제공하는 서비스가 수행될 것을 요구한다.
![System Call](/assets/posts/bd047c08b7153b986b2222a3c80f445bbdf14c1ef0ecf865287d423cb3479a51.png)

> ### I/O Structure (입출력구조) (p.15 참조) 

#### 동기식 입출력과 비동기식 입출력(p. 550참조)
- 동기식 장치는 시스템의 다른 측면과 조율하여 일정한 응답시간을 보인다. 반면 비동기식 장치는 다른 이벤트와 조율 없이 불규칙한 혹은 예측 불가능한 응답시간을 보인다.
- 동기식 입출력(Synchronous I/O)
  - I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
  - 구현방법 1
    - I/O가 끝날 때까지 CPU 낭비시킬 수 있음
    - 매 시점 하나의 I/O만 일어날 수 있음
  - 구현방법 2
    - I/O가 완료될 때까지 해당 프로그램에서 CPU 빼앗음
    - I/O처리를 기다리는 줄에 그 프로그램을 줄세움
    - 다른 프로그램에게 CPU 제공
  - 비동기식 입출력(Asynchronous I/O)
    - I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감
    
 **+** 추가 설명    
  - 입출력을 요청한 프로세스가 입출력이 끝날 때까지 대기상태일 경우 동기식 입출력.
  - 입출력을 요청한 프로세스가 종료시까지 대기하지 않고 CPU가 새로운 instruction를 실행할 경우 비동기식 입출력.
    
  #### * 두 경우 모두 I/O의 완료는 인터럽트로 알려줌
  ![동기식 입출력과 비동기식 입출력](/assets/posts/833b7f12c2d653518183f5c13a337bc65a5e8dbed671f3fb851f912dd5f7d976.png)

#### I/O Device Controller

- 해당 I/O 장치 유형을 관리하고 제어하는 일종의 작은 CPU – cpu보다 device가 처리 속도가 느리기 때문에 이러한 처리를 관리해주는 역할을 device controller가 수행함.
- 제어 정보를 위해 control register, status register를 가짐 (CPU의 지시를 위한 register) – cpu안에는 memory보다 빠른 저장 공간이 있음. 이를 register라고 함.
- local buffer를 가짐 (일종의 data register), local buffer는 일종의 각 디바이스 별 작업 공간.
- I/O는 실제 Device와 Local Buffer 사이에서 일어남.
- Device Controller는 I/O 가 끝났을 경우 interrupt로 CPU에 그 사실을 알림
- device driver (장치 구동기) : OS Code 중 각 장치별 처리 루틴을 의미함 -> Software 영역
- device Controller (장치 제어기) : 각 장치를 통제하는 일종의 작은 CPU -> Hardware

#### DMA (Direct Memory Access) 직접 메모리 엑세스 (p.15, 546 참조) 
- 인터럽트 구동 I/O의 형태는 소량의 데이터를 이동하는데는 좋지만 NVS I/O와 같은 대량 데이터 이동에 사용될 때 높은 오버헤드를 유발할 수 있다. 
- 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
- CPU의 중재 없이 device controller가 device의 buffer storage의 내용을 메모리에 block 단위로 직접 전송
- 바이트 단위가 아니라 block 단위로 인터럽트를 발생시킴
![DMA](/assets/posts/21acbf48e5b7932a27136c75a2a936c427b19d3a25c0ea5710e500cf51dee00c.png)


#### 서로 다른 입출력 기계어
- I/O를 수행하는 sepcial instruction에 의해
- Memory Mapped I/O에 의해
![서로 다른 입출력 기계어](/assets/posts/87813fb9f1d2d908782f9a578f62e6693f6ec32b89f69c47f3261790542f0f48.png)


> ### 저장장치 계층 구조  (p.14 참조) 

- internal 기억장치들은 주로 휘발성 기억장치들, external 기억장치들은 주로 비휘발성 기억장치로 구성된다.
- CPU가 직접 접근 가능한 primary 저장장치들은 register, cache memory, main memory 이렇게 세가지 이다.
- 용량이 적을수록 속도도 빠르고 가격이 비싸며, 용량이 클수록 속도도 느리고 가격이 저렴하다.
- register와 main memory간의 속도 차이를 줄이기 위해 cache memory 를 사용한다.
![저장장치 계층 구조](/assets/posts/12e45317954bf0995313f80364653598899655c384c153485e3998665652fa67.jpg)

---
> ### 프로세스 관리 (Ch3)

#### 프로그램 실행(Memory Load)
![프로그램 실행](/assets/posts/99842bb86c7086002754d541eee62834b258399d2560d203420108e0c9255a62.png)
- 각 프로그램마다 독자적인 주소 공간이 생기며, 그 안에는 stack, data, code로 영역이 나누어져 있다.
- `stack` : 함수를 호출하거나 리턴할때 데이터를 임시 보관하는 영역
  - 사용자 프로그램마다 커널 스택을 따로 사용함.
- `data` : 변수, 전역 변수 저장. 프로그램이 사용하는 자료구조 영역.
  - 프로세스를 관리하기 위한 자료구조(PCB) 저장
  - CPU, Memory, Disk를 관리하기 위한 자료구조 저장.
- `code` : 프로그램의 기계어 코드를 저장하는 영역. 커널 코드
  - 시스템 콜, 인터럽트 처리 코드
  - 자원 관리를 위한 코드
  - 편리한 서비스 제공을 위한 코드

#### 커널 주소 공간의 내용
![커널 주소 공간의 내용](/assets/posts/21a8ab1bc86e710cde6522581a044a7165111bc9041d380f7b8fcf99f280ada8.png)

- 코드 : 커널 코드
  - 시스템 콜, 인터럽트 처리 코드
  - 자원 관리를 위한 코드
  - 편리한 서비스 제공을 위한 코드
- 데이터 : 모든 하드웨어 관리하는 자료구조들과 모든 프로세스들 관리하는 자료구조(PCB)가지고 있다.
- 스택 : 각 프로세스별 커넉 스택이 들어간다

#### 사용자 프로그램이 사용하는 함수
- **함수** (function)
  - 사용자 정의 함수
    - 자신의 프로그램에서 정의한 함수
  - 라이브러리 함수
    - 자신의 프로그램에서 정의하지 않고 갖다 쓴 함수
    - 자신의 프로그램의 실행 파일에 포함되어 있다.
  - 커널 함수
    - 운영체제 프로그램의 함수
    - 커널 함수의 호출 = 시스템 콜
    ![함수](/assets/posts/afd7991ca403750914bed8e523ae41f7aeda6c185f93965ac24c1564935d6544.png)

#### 프로그램의 실행

![프로그램의 실행](/assets/posts/3e3a3e1833263145541ad54d375170289d6b9ff6deeb10025a5b150c2c4f7af6.png)

- 내가 정의한 함수 or 라이브러리 함수 실행 : 내 주소공간에 있는 함수가 사용자 모드에서 실행됨
- system call : CPU제어권이 운영체제에 넘어가서, 커널모드에서 운영체제 주소공간에 있는 코드가 실행됨

#### 프로세스의 개념
- Process is a program in execution (실행중인 프로그램)
- 프로세스의 문맥(context)
  - CPU의 수행 상태를 나타내는 하드웨어 문맥.
    - Program Counter
    - 각종 Register
  - 프로세스의 주소 공간
    - code, data, stack
  - 프로세스 관련
    - PCB (Process Control Block)
    - Kernel stack
    
#### 프로세스의 상태 (p.121 참조)
![프로세스의 상태](/assets/posts/82578768f5eeaa6fcdf9823a29406d2da913ff502d8b83ad3935fbbedd9a1783.png)
- 프로세스는 상태가 변경되면서 수행된다.
  - New(새로운)
    - 프로세스가 생성중인 상태
  - Running(실행)
    - CPU를 잡고 Instructionm을 수행중인 상태
  - Ready(준비)
    - CPU를 기다리는 상태 (메모리 등 다른 조건을 모두 만족하고)
  - Blocked (waiting, sleep) (대기)
    - CPU를 주어도 당장 instruction을 수행할 수 없는 상태
    - Process 자신이 요청한 event (ex. I/O) 가 즉시 만족되지 않아 이를 기다리는 상태 ex) 디스크에서 파일을 얽어와야 하는 경우
    - =>자신이 요청한 event 완료시 Ready
  - Terminated(종료)
    - 수행(execution)이 끝난 상태
  - Suspended (Stopped) -> 중기 스케쥴러로 인해 발생한 상태
    - CPU에서 뿐만 아니라 외부적인 이유로 프로세스의 수행이 정지된 상태
    - 프로세스는 통째로 디스크에 swap out 된다. ex) 사용자가 프로그램을 일시 정지 시킨 경우 (break key) 시스템이 여러 이유로 프로세스를 잠시 중단시킴 (메모리에 너무 많은 프로세스가 올라와 있을 때)
    - 외부적인 이유로 프로세스 정지 - 외부에서 resume해주어야 Active

#### 프로세스 상태도
![프로세스 상태도](/assets/posts/76f39afde5d86dbab1378297cb95b243f351a5be3273f64df27da8e91263649f.png)

#### Process Control Block(PCB) (p.121 참조)
- 운영체제가 각 프로세스를 관리 및 제어하기 위해 프로세스당 유지하는 상태 정보를 저장해놓은 자료구조
- 프로세스가 진행됨에 따라 PCB는 변경되기도 하며, 프로세스가 종료되면 PCB도 사라지는 것이 특징이다.
- 운영체제가 PCB에 빠르게 접근하기 위해 Process Table 을 사용하며, PID를 통해 원하는 PCB 위치에 쉽게 접근할 수 있다.

***구성 요소***
- (1) **OS**가 관리상 사용하는 정보
  - **Pointer** (프로세스의 현재 위치)
  - **Process State** (생성, 준비, 실행, 대기, 종료의 프로세스의 상태)
  - **Process number** (PID, 프로세스를 식별하기 위한 고유한 ID)
  - **Scheduling Information, priority**
- (2) **CPU** 수행 관련 하드웨어 값
  - **Program counter** (실행 될 다음 명령어의 주소)
  - **registers** (CPU 레지스터 Infomation)
- (3) 메모리 관련 (memory limits)
  - **Code**, **data**, **stack** 등의 운영체제 메모리 관리 및 위치 정보를 포함하며, 페이지 및 세그먼트 테이블 등을 저장
- (4) 파일 관련 (list of open files)
  - **Open file descriptors**(프로세스 실행을 위해 열린 파일 목록)
  - 그 외 리소스 관련 정보들
![Process Control Block(PCB)](/assets/posts/8cf335bfbaf04a5ac8bf8dd6a70b840193d2877d9cde503da5d14f430da3c37c.png)

#### Context Switch (문맥교환) (p.127 참조)
- CPU코어를 다른 프로세스로 교환하려면 이전의 프로세스의 상태를 보관하고 새로운 프로세스의 보관된 상태를 복구하는 작업이 필요하다.
- CPU를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- CPU가 다른 프로세스에서 넘어갈 때 운영체제는 다음을 수행
  - CPU를 내어주는 프로세스의 상태를 그 프로세스의 PCB에 저장
  - CPU를 새롭게 얻는 프로세스의 상태를 PCB에서 읽어 Register에 적재함.
- CPU를 비롯한 자원을 이미 프로세스가 사용중인 상태에서 다른 프로세스가 자원을 사용해야 할 경우에 프로세스의 상태(문맥 / Context)를 보관하고 새로운 프로세스의 상태를 적재하는 것을 말함

- System call이나 Interrupt 발생시 반드시 context switch가 일어나는 것은 아님.
(1) 사용자 프로세스 A(user mode) => interrupt or system call => ISR or system call 함수 (kernel mode) => 문맥교환 없이 user mode 복귀 => 샤용자 프로세스 A (user mode)
(2) 사용자 프로세스 A(user mode) => timer interrupt or I/O 요청 system call =>  kernel mode => context switch 발생 => 사용자 프로세스 B (user mode)
=> (1)의 경우에도 CPU 수행 정보 등 context의 일부를 PCB에 save 해야 하지만, 문맥교환을 해야하는 (2)의 경우 훨씬 그 부담이 큼. (ex) cache memory flush)

#### 프로세스를 스케줄링하기 위한 큐 (p.123 참조)
- Job Queue
  - 현재 시스템 내에 있는 모든 프로세스의 집합
  - Ready Queue, Device Queue가 모두 Job Queue 내에 포함되어 있음.
- Ready Queue
  - 현재 메모리 내에 있으면서 CPU를 잡아서 실행되기를 기다리는 프로세스의 집합
- Device Queue
  - I/O device의 처리를 기다리는 프로세스의 집합
- 프로세스들은 각 큐들을 오가며 수행된다.
![준비 큐와 다양한 입출력 장치 대기 큐](/assets/posts/8d94c2fcab334dc66838fcfe57f15958a3085452f2016757e6184eb7b5247da9.png)

![프로세스 스케줄링 큐잉 다이어그램](/assets/posts/60cfc7029790a9e596d961028c202500ffb02da3d76d63286fdc83aaf352a596.png)




#### Scheduler(스케쥴러) (p.123 참조)
- **`Long-term scheduler`** (Job scheduler or 장기 스케쥴러)
  - 시작 프로세스 중 어떤 것들을 **ready queue**로 보낼 지 결정
  - 프로세스에 **memory** 및 각종 자원을 주는 문제를 다루는 스케쥴러
  - _degree of Multiprogramming_(메모리에 프로그램이 몇 개 올라가 있는가?) 을 제어
  - time sharing system에는 보통 장기 스케쥴러가 존재하지 않음. (무조건 ready 상태)
- **`Short-term scheduler`** (CPU scheduler or 단기 스케쥴러)
  - 어떤 프로세스를 다음 번에 **running** 시킬지 결정
  - 프로세스에 **CPU**를 주는 문제
  - 짧은 시간 단위로 스케쥴러가 이루어짐. 그러므로 충분히 빨라야 함 (millisecond 단위)
- **`Medium-term scheduler`** (Swapper or 중기 스케쥴러)
  - 여유 공간 마련을 위해 프로세스를 통째로 메모리에서 디스크로 쫒아냄
  - 프로세스에게서 **memory를** 뺏는 문제
  - 중기 스케쥴러가 CPU 사용에서는 효율적 역할을 수행함.
  - _degree of Multiprogramming_(메모리에 프로그램이 몇 개 올라가 있는가?) 을 제어

#### 상세 프로세스 상태도 ( With active and inactive && User mode Running vs monitor mode Running)
![상세 프로세스 상태도](/assets/posts/66e3d2510d22233148c339fc84eca4fc7b543556d47847fafc08d3a52ab40607.png)

#### Thread
![Thread](/assets/posts/edd4907467867f5a1cc368928a9d808d9156f3e812e85bb22e6361021f1017ef.png)
- CPU 수행단위
- "A thread(or lightweight process) is a basic unit of CPU utilization"
- Thread의 구성
  - program counter
  - register set
  - stack space
- Thread가 동료 thread와 공유하는 부분(=task)
  - code section
  - data section
  - OS resources
- 전통적인 개념의 heavyweight process는 하나의 thread를 가지고 있는 task로 볼 수 있다.

- 장점
  - 다중 스레드로 구성된 task 구조에서는 하나의 서버 스레드가 blocked(waiting) 상태인 동안에도 동일한 테스크 내의 다른 스레드가 실행(Running)되어 빠른 처리를 할 수 있다.
  - 동일한 일을 수행하는 다중 스레드가 협력하여 높은 처리율(throughput)과 성능 향상을 얻을 수 있다.
  - 스레드를 사용하면 병렬성을 높일 수 있다. 각 thread들이 여러 개의 CPU에서 동시 실행되게 되면 빠르게 작업을 수행할 수 있음 .
  ![Thread](/assets/posts/b3dc926dc6c2fd7994104d07bcbf4ff1e9941e8b2d414df1ddffe5419dab553d.png)
- **`Responsiveness`** (응답성)
  - 여러개의 쓰레드를 사용하게 되면, 여러 프로세스의 작업을 동시에 수행할 수 있기 때문에 응답이 빨라진다.
  - ex) multi-threaded Web -> if one thread is blocked(ex. network) another thread continues(ex. display)
- **`Resource Sharing`** (자원 공유)
  - 쓰레드는 하나의 프로세스로 그 안의 CPU의 수행 단위만을 여러개 두는 차원이므로 자원을 공유하면서 더 많은 작업을 수행할 수 있다는 장점이 있다.
  - n threads can share binary code, data, resource of the process
- **`Economy`**
  - 쓰레드간의 switching은 오버헤드 없이 빠르게 동작이 가능하기 때문에 훨씬 경제적이다.
  - 프로세스를 creating or switching 하는 것보다 훨씬 오버헤드가 적음 (Solaris의 경우 각각 30배, 5배의 오버헤드 차이가 발생함)
  - creating & CPU switching thread (rather than a process)
  - Solaris의 경우 위 2가지 overhead가 각각 30배, 5배 빠름
- **`Utilization of Multi Process Architectures`**
  - 멀티 프로세서 환경에서 여러개의 쓰레드를 병렬적으로 수행할 수 있으므로 훨씬 효율적임.
  - each thread may be running in parallel on a different processor
작성할 목록

#### Implementation of Threads
- Kernel Threads
  - 커널이 Thread를 관리. 시스템 스케쥴러가 커널 스레드를 관리한다.
  - Windows
  - Solaris
  - Digital UNIX, Mach
- Library 지원 (User Threads)
  - OS는 Thread에 대해 모르고, Libary(사용자) 차원에서 thread 관리하는 방식이므로 구현에 제약이 있음.
  - POSIX Pthreads
  - Mach C-Threads
  - Solaris Threads
  - Real-time Threads


### 추가 학습
#### Mode bit 저장위치


### 출처(참고문헌)
***[Where is the mode bit?](https://stackoverflow.com/questions/13185300/where-is-the-mode-bit)***