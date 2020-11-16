#  1. 운영체제

## 1.1. Operating Systems

  ### 1.1.1. Kernel mode & User mode
    - Mode bit를 통해 구분 (1이면 uesr / 0이면 kernel)
    - I/O 처리 등 운영체제가 수행할 작업이 발생하면 Interrupt를 걸어서 운영체제에 작업 요청
    - 운영체제에게 작업을 요청하는 interface를 **System Call**이라고 부름

## 1.2. Computer Organizations
  1. Interrupts
  2. Storage Structure
  3. I/O Structure

### 1.2.1. Interrupt
  #### 1.2.1.1. 외부 Interrupt
    - I/O Interrupt
      - 입출력장치가 CPU를 부를 때 사용
    - 타이머 Interrupt
      - 타이머가 만료될 경우(Expire), 현재 프로세스 상태를 저장하고 timer interrupt 처리를 한다.
    - 전원 Interrupt
      - 갑자기 전원이 꺼졌을 경우, 여분의 배터리를 사용해 메모리가 날아가는 상황을 방지

  #### 1.2.1.2. 내부 Interrupt
    - Exception interrupt(Trap)
      - CPU에서 작업을 처리하는 중, 명령어 수행 권한이 없을 경우 자기 자신에게 거는 Interrupt (예외상황)
      - ex) Divid by zero
    - S/W Interrupt
      - 대표적으로 Supervisor call이 있다.
      - Application이 시스템에 직접적으로 접근하는 것을 방지하는 supervisor(kernel)를 호출해야 하는 상황에 발생한다.
      - 일종의 I/O protection


## 1.3. Process
  > A process is a program in execution
  ### 1.3.1. Program
  - Program은 실행파일(.exe)
  ### 1.3.2. Process
  - Instance : Program을 실행 시키는 실행 주체
  - Process는 Program을 실행했을 때 CPU를 차지하면서 수행하는 수행 주체.
  - Process는 프로세서에 의해 수행되는 프로그램 단위로 현재 실행 중이거나 곧 실행 가능한 PCB을 가진 프로그램
  - Process 목록을 보기 위해선 ps -el을 실행하면 된다.
  - **Process는 수행과 스케줄링의 가장 기본 단위이다.**
    
  구조|설명
  :----:|:---:
  Stack|동적할당 메모리
  Heap|동적할당 메모리
  Data|전역변수
  Text|컴파일된 코드

  - Heap은 프로세스가 돌아가는 런타임에 변수, 객체의 크기를 알려줌으로써 영역을 확보
  - Stack은 컴파일 타임에 지역변수나 매개 변수가 차지하는 공간

  - Process가 다른 Process를 생성할 때 두가지 경우가 있다.
    1.  부모 Process가 자식 Process와 동시에 실행되는 경우
    2.  부모 Process가 자식 Process가 실행을 끝낼 때까지 기다리는 경우

  OS p.118
  ```C
  #include <sys/types.h>
  #include <stdio.h>
  #include <unistd.h>
    int main()
  {
    pid t pid;
    /* fork a child process */
    pid = fork();
    if (pid < 0) { 
      /* error occurred */
      fprintf(stderr, "Fork Failed");
    return 1;
    }
    else if (pid == 0) { /* child process */
      /*생성된 자식 프로세스는 pid를 초기화 하지 않았기 때문에 pid가 0이 된다.*/
      execlp("/bin/ls","ls",NULL);
    }
    else { /* parent process */
      /* parent will wait for the child to complete */
      wait(NULL);
      printf("Child Complete");
    }
    return 0;
  }
  ```

  #### 1.3.2.1. Process의 상태
  > *new : The process is being created.*

  > *terminated : The process has finished execution.*

  > *running : Instructions are being executed*

  >  *waiting : The process is waiting for some event to occur*

  > *ready : The process is waiting to be assigned to a process*

    - new: 운영체제가 process를 만들 때 임시적으로 있는 state
    - running: CPU에서 수행 되고 있는 상태
    - ready: CPU에 다른 process가 돌고 있기 때문에 기다리는 상태
    - waiting: I/O 또는 다른 이벤트가 발생하기를 기다리는 상태

    - Process termination
      - 부모 프로세스는 여러 이유로 자식 프로세스를 강제 종료할 수 있다.
        1. 자식 프로세스가 할당 된 자원을 초과해서 사용할 경우
        2. 자식 프로세스에게 할당 된 task가 더 이상 필요하지 않을 경우
        3. 부모 프로세스를 종료 할 경우
   
      - Cascading termination
        - 부모 프로세스를 종료 할 경우, 모든 자식 프로세스를 먼저 종료한 후에 종료한다. 
        - Zombie process : 자식 프로세스가 끝났지만, 부모 프로세스에서 자식 프로세스의 종료상태를 회수하지 않은 경우
        - Orphan process : 부모 프로세스가 끝났지만, 자식 프로세스를 종료하지 않은 경우


  #### 1.3.2.2. PCB
    > 운영체제가 프로세스를 제어하기 위해 정보를 저장해 놓는 곳으로, 프로세스의 상태 정보를 저장하는 구조체
    > 프로세스 상태 관리와 Context switching을 위해 필요하다.
    > PCB는 프로세스 생성 시 만들어지며 주기억장치에 유지된다.

  ##### 1.3.2.2.1. PCB에 유지되는 정보
    1. PID: 프로세스의 고유 번호
    2. 상태: 준비, 대기, 실행 등의 상태
    3. 포인터: 다음 실행될 프로세스의 포인터
    4. Register save area: 레지스터 관련 정보
    5. Priority: 스케줄링 및 프로세스 우선순위
    6. 할당된 자원 정보
    7. Account: CPU 사용시간, 실제 사용된 시간
    8. 입출력 상태 정보

  #### 1.3.2.3. Context Switching
    - 프로세스를 실행하는 과정의 프로세스 정보는 CPU 내의 레지스터에 저장하고 있다.
    - Swapping
    - PCB에 수행되던 레지스터 값들이 저장된다.

  #### 1.3.2.4. IPC(Inter-Process Communication)
    - Cooperating process 사이에서 데이터를 주고 받는 행위
    
  ##### 1.3.2.4.1. Cooperating process를 사용하는 이유
      1. Information sharing
      2. Computation speedup
      3. Modularity
      4. Convenience

  ##### 1.3.2.4.2. Message passing
      - OS가 memory protection을 위해 대리 전달해주는 것
      - 안전하고 동기화 문제가 없다.
      - 성능면에서 떨어진다.
      - Direct/Indirect Communication이 있다.
        - Direct communitaion은 커널에 메시지를 직접 주고 커널이 직접 전달하는 방식
        - Indirect communication은 커널의 메시지 박스에 메시지를 넣어놓고 다른 프로세스가 확인하는 방식
    
  ##### 1.3.2.4.3. Shared Memory
    - 두 프로세스 간의 공유된 메모리를 생성 후 이용
    - 동기화 문제 발생 가능성 있음
    
  ##### 1.3.2.4.4. 생산자-소비자 문제
    - 일반적으로 생산자가 정보를 생산하는 속도가 소비하는 속도보다 빠르기 때문에 발생하는 문제
    - 사용되는 버퍼가 무한한 경우(Unbounded buffer) : 버퍼 크기의 제한이 없기 때문에 생산자는 계속해서 새로운 정보를 제공할 수 있다.
    - 사용되는 버퍼가 유한한 경우(Bounded buffer) : 버퍼의 크기에 제한이 있어 생산자가 정보를 제공할 때, 버퍼의 용량을 확인하고 대기해야할 가능성이 있다.

## 1.4. Thread
  ### 1.4.1. Multi-Threading의 장점
    1. 프로세스 내의 자원을 공유하기 때문에 IPC 등에 필요한 overhead를 줄일 수 있다. 
    2. 자원을 할당하는 시스템 콜이 줄어들어 자원을 효율적으로 관리할 수 있다.
    3. 프로세스 간의 전환 속도보다 thread간 전환 속도가 빠르다.(Thread는 stack영역만 처리하기 때문) 
  
  ### 1.4.2. Java thread
    - Java에서는 thread 객체를 생성하고 Runnable을 상속받은 클래스에게 객체를 넘겨준다.
    - start() 메소드를 통해 JVM에 thread객체를 생성한다.
    - run() 메소드를 통해 JVM에 의해 실행될 수 있도록 한다.
    - join()을 통해 다른 쓰레드의 종료를 기다릴 수 있다.

  ### 1.4.3. Thread-safe
    - Multi-thread 환경에서 여러 thread가 동시에 공유 자원에 접근할 때, 의도한 대로 동작하는 것
    - Critical section을 동기화 기법(Mutex, Semaphore)으로 제어해야한다.

## 1.5. Process scheduling
  ### 1.5.1. CPU burst & I/O burst
    - CPU burst: CPU 명령을 실행하는 시간 / CPU burst가 큰 프로세스 = CPU bound process
    - I/O burst: I/O를 요청한 후, 기다리는 시간 / I/O burst가 큰 프로세스 = I/O bound process
    - 사용자와 interactive한 작업들은 I/O burst가 크다.

  ### 1.5.2. Scheduling criteria
    1. CPU utilization
    2. Throughput
    3. Turnaround time
    4. Waiting time
    5. Response time

  ### 1.5.3. FCFS
    - 앞에 있는 프로세스가 처리 시간이 길다면 전체 waiting time이 길어진다.
    - (=Convoy effect)
  
  ### 1.5.4. SJF(Shortest Job First)
    - waiting time이 짧다는 장점이 있다.(CPU burst가 가장 작은 것을 먼저 수행한다.)
    - Non-preemptive 하다. (=프로세스 처리 중간에 중단할 수 없다.)
    - 프로세스가 얼마만큼의 CPU burst를 갖는지 미리 알 수 없기 때문에 실제로 적용하기 어렵다.

  ### 1.5.5. SRTF(Shortest Remaining Time First)
    - SJF와 같지만 수행시키던 프로세스를 중단시키고 다른 프로세스를 실행할 수 있다.

  ### 1.5.6. Priority Scheduling
    - 프로세스의 중요도를 기준으로 우선순위 부여
    - starvation이라는 단점이 존재한다. (무한히 기다리는 것)
    - aging을 통해 starvation을 해결한다.

  ### 1.5.7. Round-Robin
    - Time-quantum을 정하고 time-quantum마다 context switching을 통해 프로세스를 바꿔서 처리한다.
    - 적절한 quantum size가 중요하다. CPU burst의 80%를 커버할 수 있을 정도로 설정

  ### 1.5.8. Multilevel Queue
    - 어떤 프로세스냐에 따라서 여러 종류의 그룹으로 나누고 여러 개의 큐에 다양한 알고리즘을 적용

  ### 1.5.9. Multilevel Feedback Queue
    - 첫 queue의 Time quantum을 초과한 프로세스는 다음 queue로 넘겨진다.
    - 마지막 queue는 FCFS방식으로 처리된다.
    - 사용자와 interactive하지 않은 background의 프로세스는 CPU burst가 매우 크다는 특징을 이용
  
## 1.6. DeadLock

> Deadlock은 다양한 유형의 자원을 대상으로 경쟁하는 여러 프로세스 때문에 발생한다.

- Multi-Thread 환경에서는 여러 Thread에서 하나의 자원을 공유하는 경우가 많아 발생 가능성이 크다.


# 2. 네트워크

  ## 2.1. TCP connection (3 way)
    1. open()을 실행한 클라이언트가 SYN을 보내고 SYN_SENT 상태로 대기
    2. 서버는 SYN_RCVD 상태로 바꾸고 SYN과 ACK를 전송
    3. SYN/ACK를 받은 클라이언트는 ESTABLISHED 상태로 변경하고 서버에게 응답 ACK를 전송
    4. ACK를 받은 서버는 ESTABLISHED 상태로 변경

  ## 2.2. TCP disconnection (4 way)
    1. close()를 실행한 클라이언트가 FIN을 보내고 FIN_WAIT1 상태로 대기
    2. 서버는 CLOSE_WAIT로 바꾸고 응답 ACK를 전달. 동시에 해당 포트에 연결되어 있는 응용 프로그램에게 close() 요청
    3. ACK를 받은 클라이언트는 상태를 FIN_WAIT2로 변경
    4. close() 요청을 받은 서버 응용 프로그램은 종료 프로세스를 진행하고 FIN을 클라이언트에게 보내 LAST_ACK 상태로 바꿈
    5. FIN을 받은 클라이언트는 ACK를 서버에 다시 전송하고 TIME_WAIT으로 상태를 바꾼다. TIME_WAIT에서 일정 시간이 지나면 CLOSED 된다. ACK를 받은 서버도 포트를 CLOSED로 닫는다.

  ## 2.3. 기타질문
  - Q: 단계 차이가 나는 이유
    - A: client가 데이터 전송을 마쳤다고 해도 server는 아직 보낼 데이터가 남아 있을 수 있기 때문에 FIN에 대한 ACK만 보내고, 남은 데이터를 모두 전송한 후에 FIN 메세지를 보내기 때문이다.
  - Q: Server에서 FIN을 전송하기 전에 전송한 패킷이 Routing 지연이나 패킷 유실로 인한 재전송으로 인해 FIN 패킷보다 늦게 도착한다면 어떻게 될까?
    - 이런 현상에 대비해 client는 server로부터 FIN을 수신하더라도 일정시간 세션을 남겨 놓고 잉여 패킷을 기다린다.
  - 초기 Seq #을 0부터 시작하지 않고 난수를 생성하는 이유는?
    - Connection을 맺을 때 사용하는 포트는 유한 범위 내에서 사용하고 시간이 지남에 따라 재사용된다. 따라서 두 통신 호스트가 과거에 사용된 포트 번호 쌍을 사용할 가능성이 존재한다.

  ## 2.4. TCP와 UDP의 공통점
    1. 포트번호를 이용하여 주소를 지정한다.
    2. 데이터 오류 검사를 위한 checksum이 존재한다.
    

  ## 2.5. TCP와 UDP의 차이점
    1. 연결형/비연결형
    2. 데이터의 경계를 구분한다/하지 않는다.
    3. 신뢰성 있는/없는 데이터 전송(데이터의 재전송)
    4. 일대일/일대일, 일대다, 다대다 통신

  ## 2.6. HTTP protocol
    - 웹 상에서 클라이언트와 서버 간의 요청/응담으로 정보를 주고 받을 수 있는 프로토콜
    - TCP와 UDP를 사용하며 80번 포트를 사용한다.
      - 비연결: 클라이언트가 요청을 서버에 보내고 서버가 적절한 응답을 클라이언트에 보내면 바로 연결이 끊긴다.
      - 무상태: 연결을 끊는 순간 클라이언트와 서버의 통신은 끝나며 상태 정보를 유지하지 않는다.

  ## 2.7. HTTPS
    - 443번 포트
    - 소켓 통신에서 일반 텍스트를 이용하는 대신에, 웹 상에서 정보를 암호화하는 SSL이나 TLS 프로토콜을 통해 세션 데이터를 암호화한다.
    - 기밀성, 데이터 무결성, ID 및 디지털 인증서를 사용한 인증을 제공
    - 공개키 암호화
      - 공개키: 모두에게 공개
      - 개인키: 개인에게만 공개 
  
  ## 2.8. HTTP 헤더 내 Entity Header 항목
    1. Content-Type: MIME 미디어 타입, 인코딩 방식 (Content-Type: text/html; charset-latin-1)
    2. Content-Language: 해당 개체와 가장 잘 어울리는 사용자 언어
    3. Content-Encoding: 해당 개체의 데이터 압축 방식 (Content-Encoding: gzip, deflate)
    4. Content-Length: 전달되는 해당 개체의 바이트 길이 또는 크기
    5. Content-Location: 해당 개체의 실제 위치
    6. Content-Disposition: 응답 Body를 브라우저가 어떻게 표시해야하는지 (inline이면 웹화면에 표시, attachment인 경우 다운로드)
    7. ...

  ## 2.9. HTTP 헤더 내 Request Header 항목
    1. Host: 요청하는 호스트에 대한 호스트명 및 포트번호
    2. User-Agent: 클라이언트 소프트웨어(브라우저, OS)
    3. From: 클라이언트 사용자 메일 주소
    4. Cookie: 서버에 의해 Set-Cookie로 클라이언트에게 설정된 쿠키 정보
    5. Referer: 바로 직전에 머물렀던 웹 링크 주소
    6. ...

  ## 2.10. HTTP 헤더 내 Response Header 항목
    1. Server: 서버 소프트웨어 정보
    2. Accept-Range
    3. Set-Cookie: 서버 측에서 클라이언트에게 세션 쿠키 정보 설정
    4. Expires: 리소스가 지정된 일시까지 유효
    5. ...

  ## 2.11. HTTP 동작 과정
  > 서버 접속 -> 클라이언트 -> 요청 -> 서버 -> 응답 -> 클라이언트 -> 연결 종료

    1. 사용자가 웹 브라우저에 URL 주소 입력
    2. DNS 서버에서 웹 서버의 호스트 이름을 IP 주소로 변경 요청
    3. 웹 서버와 TCP 연결 시도 (3 way handshaking)
    4. 클라이언트가 서버에게 요청
      - HTTP Request Message = Request Header + 빈줄 + Request Body
      - Request Header
        + 요청 메소드 + URI + HTTP 프로토콜 버전
        + ex) GET /background.png HTTP/1.0 || POST / HTTP 1.1
        + GET, HEAD, DELETE, OPTIONS 처럼 리소스를 가져오는 요청은 바디 미포함
    5. 서버가 클라이언트에게 데이터 응답
      - HTTP Response Message = Response Header + 빈줄 + Response Body
        + HTTP 프로토콜 버전 + 응답 코드 + 응답 메세지
        + ex) HTTP/1.1 404 Not Found
    6. 서버 클라이언트 간 연결 종료
    7. 웹 브라우저가 웹 문서 출력

  ## 2.12. HTTPS 동작 과정
    1. 클라이언트가 서버에 접속하여 Handshaking 과정에서 서로 탐색
       1. Client Hello
          - 클라이언트가 서버에게 전송할 데이터
            + 클라이언트 측에서 생성한 랜덤 데이터
            + C/S 암호화 방식 통일을 위해 클라이언트가 사용할 수 있는 암호화 방식
            + 기존 세션을 재활용하기 위한 세션 아이디
       2. Server Hello
          - Client Hello에 대한 응답으로 전송할 데이터
            + 서버 측에서 생성한 랜덤 데이터
            + 서버가 선택한 클라이언트의 암호화 방식
            + SSL 인증서
       3. Client 인증 확인
          - 서버로부터 받은 인증서가 CA에 의해 발급되었는지 목록에서 확인하고 CA 공개키로 인증서 복호화
          - 랜덤 데이터를 조합하여 pre master secret 값 생성(대칭키 암호화에 사용할 키)
          - pre master secret 값을 공개키로 서버에 전달
          - session key 생성
       4. Server 인증 확인
          - 서버는 비공개키로 복호화해 pre master secret 값 취득
          - session key 생성
       5. Handshaking 종료

    2. 데이터 전송
       1. 서버와 클라이언트는 session key를 활용해 데이터를 암호화해 데이터 송수신
    3. 연결 종료 및 session key 폐기

  ## 2.13. GET POST Method
    - HTTP 프로토콜을 이용해 서버에 데이터(요청 정보)를 전달할 때 사용하는 방식
  
    ### GET Method
      - 정보를 조회하기 위한 method
      - 서버에서 데이터를 가져와서 보여주기 위한 method
      - Select
      - URL 끝에 ? 와 함께 key=value 쌍으로 요청 정보 전송
      - URL에 요청 정보를 붙여서 전송
      - 대용량 데이터 전송이 힘들다(parameter 길이 제한)
      - 보안상 취약하다
      - Body는 비어있다.
      - POST 방식보다 빠르다. (caching을 사용하기 때문)

    ### POST Method
      - 서버의 값이나 상태를 바꾸기 위한 method
      - Insert/Update/Delete
      - HTTP 패킷의 body안에 요청 정보를 숨겨서 전송
      - Request Header의 Content-Type에 해당 데이터 타입이 표현 됨
        - Default: application/octet-stream
        - Text: text/plain
        - File: multipart/form-date
      - 대용량 데이터 전송에 적합
      - 보안상 안전하다.




