-----
### 비동기 I/O
-----
1. 여러 현대의 운영체제들은 I/O 요청을 디스크로 내려 보낼 수 있는 비동기 I/O (Asynchronous I/O)라고 불리는 새로운 방법 개발
2. 이 인터페이스는 프로그램이 I/O 요청을 하면 I/O 요청이 끝나기 전 제어권을 즉시 다시 호출자에게 돌려주는 것을 가능하게 했으며 추가적으로 여러 종류의 I/O들이 완료가 되었는지도 판단할 수 있도록 함
3. 예를 들어, Mac OS X가 제공하는 인터페이스는 다음과 같음 (다른 운영체제도 비슷한 API 제공)
   - 이 API는 struct aiocb 또는 전문 용어로 AIO 제어 블럭(AIO Control Block)이라고 불리는 기본 구조 사용
```c
struct aiocb {
      int              aio_fildes;     /* File descriptor */
      off_t            aio_offset;     /* File offset */
      volatile void    *aio_buf;       /* Location of buffer */
      size_t           aio_nbytes;     /* Length of transfer */
};
```
  - 파일에 대한 비동기 읽기 요청을 하려면 응용 프로그램이 먼저 이 자료 구조에 읽고자 하는 파일의 파일 디스크럽터(aio_fildes), 파일 내 위치(aio_offset)와 더불어 요청의 길이(aio_nbytes), 마지막으로 읽기 결과로 얻은 데이터를 저장할 대상 메모리 위치(aio_buf)와 같은 관련 정보를 채워 넣어야 함
  - 이 자료구조에 정보가 다 채워지면 응용 프로그램은 읽으려는 파일에 비동기 호출을 보냄
  - Mac OS X에서는 간단한 비동기 읽기(Asynchronous Read) API를 사용
```c
int aio_read(struct aiocb *aiocbp);
```
  - 이 명령어를 통해 I/O 호출을 성공하면, 즉시 리턴을 하며 응용 프로그램(이벤트 기반 서버 류)은 하던 일을 계속 진행 가능

4. I/O가 종료되었음을 아는 방법과 aio_buf가 가리키는 버퍼에 요청했던 데이터가 있는 것을 아는 방법
   - Mac OS X에서는 이 API를 aio_error()라고 함
```c
int aio_error(const struct aiocb *aiocbp);
```
   - 이 시스템 콜은 aiocbp에 의해 참조된 요청이 완료되었는지 검사
   - 완료되었다면 성공했다고 리턴을 하고(0으로 표시) 실패했다면 EINPROGRESS를 반환
   - 모든 대기 중인 비동기 I/O는 주기적으로 aio_error() 시스템 콜로 시스템에 폴링(poll)하여 해당 I/O가 완료되었는지 확인

5. 어떤 시점에 수십 또는 수백 개의 I/O를 요청하는 프로그램에 대해 많은 요청을 검사할 지, 일정 시간 대기 할지 또는 다른 방법
   - 어떤 시스템들은 인터럽트 기반 접근법 제공
   - 유닉스의 시그널(Signal)을 사용하여 비동기 I/O가 완료되었다는 것을 응용 프로그램에게 알려주므로 시스템에 반복적 완료 여부를 확인할 필요 없음 (폴링 대 인터럽트 문제는 I/O 장치들을 다룰 때 나타남)

6. 비동기 I/O가 없는 시스템에서는 제대로 된 이벤트 기반 접근법을 구현할 수 없지만, 네트워크 패킷을 처리하기 위해 이벤트를 사용하고 대기 중인 I/O들을 처리하기 위해 쓰레드 풀을 사용하는 하이브리드 기법 제안

-----
### 유닉스 시그널 (Signal)
-----
1. 시그널 (Signal) : 프로세스 간 통신 방법으로, 응용 프로그램에게 시그널이 전달되면 해당 응용 프로그램은 하던 작업을 중지하고 시그널 핸들러(Signal Handler)를 실행
   - 시그널 핸들러는 프로그램이 정의한 시그널 처리 코드로, 시그널 처리가 완료되면 프로세스는 이전 작업 재개
   - 시그널 마다 이름 존재
     + HUP(Hang UP의 약자, 끊다)
     + INT(Interrupt의 약자, 인터럽트)
     + SEGV(Segmentation Violation의 약자, 세그먼트 위반) 등

   - 커널 자체도 시그널 전송의 주체가 되기도 함
     + 예를 들어, 프로그램이 세그먼트 위반을 하면 운영체제는 SIGSEGV(SIG : 시그널 이름 앞에 붙는 접두어)를 보냄
     + 프로그램이 시그널을 접수할 수 있도록 설정되었다면 (시스템 콜을 사용하여 프로세스가 특정 시그널을 받지 못하도록 설정 가능한데 이런 시그널을 Masking 한다고 함) 프로그램이 오류가 발생시켰을 때, 특정 코드를 실행하도록 만들 수 있음 (디버깅 시 유용)

   - 대부분 프로세스들은 각 시그널들에 대한 처리코드가 정의되어 있음
   - 정의되지 않은 시그널을 받으면 프로세스는 기본 동작을 수행 (SEGV의 경우 프로세스 강제 종료)

2. 무한 루프를 수행하는 프로그램 (무한 루프에 들어가기 전 SIGHUP 시그널에 대한 핸들러를 먼저 설정
```c
#include <stdio.h>
#include <signal.h>

void handle(int arg) {
    printf("stop wakin' me up... \n");
}

int main(int argc, char *argv[]) {
    signal(SIGHUP , handle);
    while (1)
          ; // 시그널을 기다리는 것 외에 아무 일을 안함
    return 0;
}
```
  - 이 프로그램에 kill 명령어를 사용하여 시그널을 보낼 수 있음
  - 이 시그널이 도착하면 프로그램은 while 루프를 중단하고 handle() 함수 실행
```
prompt> ./main &
[3] 36705
prompt> kill −HUP 36705
stop wakin' me up ...
prompt> kill −HUP 36705
stop wakin' me up ...
prompt> kill −HUP 36705
stop wakin' me up ...
```
