# 1. 운영체제

## 1.1. Kernerl mode 와 User mode

- Mode bit를 통해 구분 (1이면 uesr / 0이면 kernel)
- I/O 처리 등 운영체제가 수행할 작업이 발생하면 Interrupt를 걸어서 운영체제에 작업 요청
- 운영체제에게 작업을 요청하는 interface를 **System Call**이라고 부름

## 1.2. Interrupt

- 외부 Interrupt
  - I/O Interrupt
    - 입출력장치가 CPU를 부를 때 사용
  - 타이머 Interrupt
    - 타이머가 만료될 경우(Expire), 현재 프로세스 상태를 저장하고 timer interrupt 처리를 한다.
  - 전원 Interrupt
    - 갑자기 전원이 꺼졌을 경우, 여분의 배터리를 사용해 메모리가 날아가는 상황을 방지
- 내부 Interrupt
  - Exception interrupt(Trap)
    - CPU에서 작업을 처리하는 중, 명령어 수행 권한이 없을 경우 자기 자신에게 거는 Interrupt (예외상황)
    - ex) Divid by zero
  - S/W Interrupt
    - 대표적으로 Supervisor call이 있다.
    - Application이 시스템에 직접적으로 접근하는 것을 방지하는 supervisor(kernel)를 호출해야 하는 상황에 발생한다.
    - 일종의 I/O protection

## 1.3. Process와 Program

> A process is a program in execution
### 1.3.1. Program
- Program은 실행파일(.exe)
### 1.3.2. Process
- Instance : Program을 실행 시키는 실행 주체
- Process는 Program을 실행했을 때 CPU를 차지하면서 수행하는 수행 주체
- **Process는 수행과 스케줄링의 가장 기본 단위이다.**
  
구조|설명
:----:|:---:
Stack|동적할당 메모리
Heap|동적할당 메모리
Data|전역변수
Text|컴파일된 코드

- Heap은 프로세스가 돌아가는 런타임에 변수, 객체의 크기를 알려줌으로써 영역을 확보
- Stack은 컴파일 타임에 지역변수나 매개 변수가 차지하는 공간

#### 1.3.2.1. Process의 상태
- *new : The process is being created.*
- *terminated : The process has finished execution.*
- *running : Instructions are being executed*
- *waiting : The process is waiting for some event to occur*
- *ready : The process is waiting to be assigned to a process*


## 1.4. DeadLock

> Deadlock은 다양한 유형의 자원을 대상으로 경쟁하는 여러 프로세스 때문에 발생한다.

- Multi-Thread 환경에서는 여러 Thread에서 하나의 자원을 공유하는 경우가 많아 발생 가능성이 크다.

