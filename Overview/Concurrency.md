-----
### 병행성 (Concurrency)
-----
1. 프로그램이 한 번에 많은 일을 실행하려 할 때, (즉, 동시에) 발생하는 그리고 반드시 해결해야 하는 문제들을 가리킬 때 사용
2. 병행성 문제는 운영체제 자체에서 발생하지만, 운영체제만의 문제가 아닌 멀티 쓰레드 프로그램에서도 동일한 문제를 드러냄
3. 멀티 쓰레드 프로그램
<div align="center">
<img src="https://github.com/user-attachments/assets/45e32bf4-d529-4a78-a5f1-7f553a36037d">
</div>

   - 메인 프로그램은 Pthread_creat()를 사용하여 두 개의 쓰레드를 생성
   - 각 쓰레드는 worker()를 실행하며, worker()는 loops번 만큼 루프를 반복하면서 카운터 값 증가

   - loops 변수를 1000으로 설정하여 프로그램을 실행시킬 때의 counter 변수의 값
<div align="center">
<img src="https://github.com/user-attachments/assets/07e90139-465b-4d85-b9d5-4aedbfb97364">
</div>

   - 각 쓰레드가 1000번씩 counter 값을 증가시켰으므로 counter의 최종 값은 2000
   - 100000으로 입력값을 줄 경우
<div align="center">
<img src="https://github.com/user-attachments/assets/3e69466c-65f6-4442-99e8-c16bfab54640">
</div>

   - 최종 값이 200000이 아닌 143012가 되었으며, 다시 실행하면 직전과 다른 실행 결과가 나옴

4. 명령어가 한 번에 하나씩만 실행됨
   - 앞 프로그램의 핵심 부분은 counter를 증가시키는 부분은 세 개의 명령어로 구성
     + counter 값을 메모리에서 레지스터로 탑재하는 명령어 하나
     + 레지스터를 1 증가시키는 명령어 하나
     + 레지스터의 값을 다시 메모리에 저장하는 명령어 하나
   - 이 세개의 명령어가 원자적(Atomically)으로 (한 번에 3개 모두) 실행되지 않으므로 발생
