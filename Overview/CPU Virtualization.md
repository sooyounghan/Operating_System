-----
### CPU 가상화
-----
1. Spin()을 호출하는 프로그램 (Spin() : 1초 동안 실행된 후 리턴하는 함수)으로, 이후 사용자가 명령어 라인으로 전달한 명령어 출력을 무한히 반복
<div align="center">
<img src="https://github.com/user-attachments/assets/4cbeb836-5e8e-48f4-9a96-8a2d7fecdb5f">
</div>

  - cpu.c라는 이름으로 저장하고 단일 프로세스 시스템에서 컴파일하고 실행시킨다고 가정
  - 출력 결과
<div align="center">
<img src="https://github.com/user-attachments/assets/aeb0acfb-c1d0-4474-877a-9f3dfbc96281">
</div>

   - 시스템은 프로그램 실행 후 1초가 지나면 사용자가 전달한 입력 문자열(이 경우, 문자 "A") 출력
   - 출력 후 실행을 계속하며, 프로그램은 계속 실행 됨 (Control + C를 눌러 프로그램 종료 가능)

2. 같은 작업에 대한 여러 인스턴스에서 동시에 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/2776929e-b346-4b33-9cf3-628b5113321c">
</div>

   - 프로세서가 하나 밖에 없음에도 프로그램 4개 모두 동시에 실행
     + ```&``` : tsch 셀 상 작업을 백그라운드로 실행 가능하며, 이는 사용자가 다음 명령어를 바로 실행시킬 수 있다는 것을 의미 (여기서는 다른 인스턴스 실행)
     + 명령어 사이 세므콜론 : tcsh에서 동시에 여러 프로그램을 실행시킬 수 있게 함

3. 하나의 CPU 또는 소규모 CPU 집합을 무한 개의 CPU가 존재하는 것처럼 변환하여 동시에 많은 수의 프로그램을 실행시키는 것을 CPU 가상화(CPU Vritualization)
4. 프로그램을 실행하고, 멈추고, 어떤 프로그램을 실행시킬 것인가를 운영체제에게 알려주기 위해서는 원하는 바를 운영체제에게 전달할 수 있는 인터페이스(API)가 필요
5. 특정 순간에 여러 프로그램을 실행되기를 원할 때, 실행 순서는 운영체제의 정책(Policy)에 달려 있음
<div align="center">
<img src="https://github.com/user-attachments/assets/68b5df30-cf0f-41e0-ac16-896d945fa6a3">
</div>
