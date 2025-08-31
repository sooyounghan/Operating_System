-----
### wait() 시스템 콜
-----
1. 부모 프로세스가 자식 프로세스 종료를 대기해야 하는 경우, 이러한 작업을 위해 wait() 시스템 콜 사용 (더 많은 기능을 가진 시스템 콜을 사용하기 위해서는 waitpid() 사용)
<div align="center">
<img src="https://github.com/user-attachments/assets/d5006e61-0904-4145-94da-98805f1659a9">
</div>

  - p2.c에서의 부모 프로세스는 wait() 시스템 콜을 호출하여 자식 프로세스 종료 시점까지 자신의 실행을 잠시 중지
  - 자식 프로세스가 종료되면 wait()는 리턴
  - 출력 결과
<div align="center">
<img src="https://github.com/user-attachments/assets/5372bcf7-8baf-4f0f-a81f-f43db36ce051">
</div>

  - 항상 자식 프로세스가 먼저 출력 수행되어야 함
  - 자식 프로세스가 부모 프로세스보다 먼저 실행되면 자식 프로세스가 먼저 출력 (부모 프로세스가 먼저 실행되면 곧바로 wait()를 호출)
  - 이 시스템 콜은 자식 프로세스가 종료될 때까지 리턴하지 않으므로, 따라서 부모가 먼저 실행되더라도 자식 종료 후 wait()가 리턴하며, 그런 후 부모 프로세스가 출력
