#  운영체제

## 1. Kernerl mode 와 User mode

- Mode bit를 통해 구분 (1이면 uesr / 0이면 kernel)
- I/O 처리 등 운영체제가 수행할 작업이 발생하면 Interrupt를 걸어서 운영체제에 작업 요청
- 운영체제에게 작업을 요청하는 interface를 **System Call**이라고 부름

## 2. Interrupt

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

## 3. Process와 Program

> A process is a program in execution
### 3.1. Program
- Program은 실행파일(.exe)
### 3.2. Process
- Instance : Program을 실행 시키는 실행 주체
- Process는 Program을 실행했을 때 CPU를 차지하면서 수행하는 수행 주체.
- Process는 프로세서에 의해 수행되는 프로그램 단위로 현재 실행 중이거나 곧 실행 가능한 PCB을 가진 프로그램
- **Process는 수행과 스케줄링의 가장 기본 단위이다.**
  
구조|설명
:----:|:---:
Stack|동적할당 메모리
Heap|동적할당 메모리
Data|전역변수
Text|컴파일된 코드

- Heap은 프로세스가 돌아가는 런타임에 변수, 객체의 크기를 알려줌으로써 영역을 확보
- Stack은 컴파일 타임에 지역변수나 매개 변수가 차지하는 공간

#### 3.2.1. Process의 상태
> *new : The process is being created.*

> *terminated : The process has finished execution.*

> *running : Instructions are being executed*

>  *waiting : The process is waiting for some event to occur*

> *ready : The process is waiting to be assigned to a process*

- new: 운영체제가 process를 만들 때 임시적으로 있는 state
- running: CPU에서 수행 되고 있는 상태
- ready: CPU에 다른 process가 돌고 있기 때문에 기다리는 상태
- waiting: I/O 또는 다른 이벤트가 발생하기를 기다리는 상태

#### 3.2.2. PCB
> 운영체제가 프로세스를 제어하기 위해 정보를 저장해 놓는 곳으로, 프로세스의 상태 정보를 저장하는 구조체
> 프로세스 상태 관리와 Context switching을 위해 필요하다.
> PCB는 프로세스 생성 시 만들어지며 주기억장치에 유지된다.

##### 3.2.2.1. PCB에 유지되는 정보
1. PID: 프로세스의 고유 번호
2. 상태: 준비, 대기, 실행 등의 상태
3. 포인터: 다음 실행될 프로세스의 포인터
4. Register save area: 레지스터 관련 정보
5. Priority: 스케줄링 및 프로세스 우선순위
6. 할당된 자원 정보
7. Account: CPU 사용시간, 실제 사용된 시간
8. 입출력 상태 정보

#### 3.2.3 Context Switching
- 프로세스를 실행하는 과정의 프로세스 정보는 CPU 내의 레지스터에 저장하고 있다.
- PCB에 수행되던 레지스터 값들이 저장된다.

#### 3.2.4 IPC(Inter-Process Communication)
- Cooperating process 사이에서 데이터를 주고 받는 행위
  
  ##### 3.2.4.1 Cooperating process를 사용하는 이유
  1. Information sharing
  2. Computation speedup
  3. Modularity
  4. Convenience

  ##### 3.2.4.2 Message passing
  - OS가 memory protection을 위해 대리 전달해주는 것
  - 안전하고 동기화 문제가 없다.
  - 성능면에서 떨어진다.
  - Direct/Indirect Communication이 있다.
  
  ##### 3.2.4.3 Shared Memory
  - 두 프로세스 간의 공유된 메모리를 생성 후 이용
  - 동기화 문제 발생 가능성 있음
  - 




## 4. DeadLock

> Deadlock은 다양한 유형의 자원을 대상으로 경쟁하는 여러 프로세스 때문에 발생한다.

- Multi-Thread 환경에서는 여러 Thread에서 하나의 자원을 공유하는 경우가 많아 발생 가능성이 크다.

