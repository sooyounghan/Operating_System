-----
### Test-And-Set (Atomic Exchange)
-----
1. 멀티프로세스에서는 인터럽트를 중지시키는 것이 의미가 없으므로 시스템 설계자들은 락 지원을 위한 하드웨어를 설계하기 시작
2. 하드웨어 기법 중 가장 기본은 Test-And-Set 명령어 또는 원자저 교체(Atomic Exchange)라고 불리는 기법
   - 플래그 변수를 활용한 락 구현
```c
1 typedef struct _ _lock_t { int flag; } lock_t;
2
3 void init(lock_t *mutex) {
4     // 0 −> 락 사용 가능, 1 −> 락 사용 중
5     mutex−>flag = 0;
6 }
7
8 void lock(lock_t *mutex) {
9     while (mutex−>flag == 1) // flag 변수를 검사 (TEST)
10         ; // spin−wait (do nothing)
11    mutex−>flag = 1; // 이제 설정(SET)
12 }
13
14 void unlock(lock_t *mutex) {
15     mutex−>flag = 0;
16 }
```
   - 간단한 변수를 사용해 쓰레드가 락을 획득하였는지를 나타냄
   - 임계 영역에 진입하는 첫 쓰레드가 lock()을 호출하여 플래그 값이 1인지 검사(Test)하고 (첫 번쨰 경우는 1이 아님), 플래그의 값을 1로 설정(Set)하여 이 쓰레드가 락을 보유(Hold)하고 있다고 표시
   - 임계 영역에서 나오면 쓰레드가 unlokc()을 호출하여 플래그 값을 초기화하여 락을 더 이상 보유하고 있지 않다고 표시

3. 만약, 첫 번째 쓰레드가 임계 영역 내 있을 때 다른 쓰레드가 lock()을 호출하면, 그 쓰레드는 while문으로 Spin-Wait하여 처음 쓰레드가 unlock()을 호출하여 플래그를 초기화하기를 기다림
   - 처음 쓰레드가 플래그를 초기화하면 대기하던 쓰레드는 while문에서 빠져나와 플래그를 1로 설정하고 임계 영역 내로 진입

4. 문제 - 정확성과 성능
<div align="center">
<img src="https://github.com/user-attachments/assets/ed2c5ec3-aa07-4c77-bf66-11be90cef035">
</div>

   - 정확성 : 시작할 때 코드의 flag = 0이라 가정
     + 적시에 인터럽트가 발생하면 두 쓰레드 모두 플래그를 1로 설정하는 경우가 발생할 수 있어 임계 영역에 두 쓰레드 모두 다 진입할 수 있게 됨
     + 상호 배제 제공이라는 기본 요구 조건 보장에 실패

   - 성능 : 사용 중인 락을 대기하는 방법에 문제
     + Spin-Wait라는 방법을 사용해 플래그 값을 무한히 검사하는데, 이 방법은 다른 쓰레드가 락을 해제할 때까지 시간 낭비
     + 단일 프로세서에서 특히 손해가 매우 크며, 락을 소유한 쓰레드조차 실행할 수 없음(적어도 문맥 교환 발생 전까지) 
