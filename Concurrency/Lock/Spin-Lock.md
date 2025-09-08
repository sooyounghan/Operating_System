-----
### 스핀 락 구현
-----
1. 락 구현을 위한 명령어(어셈블리 명령어)를 제공하는 시스템 존재
   - SPARC에서는 load/store unsigned byte 동작을 하는 ldstub
   - x86에서는 원자적 교체 명령어인 xchg
   - 기본적으로 동일한 일을 수행하며 일반적으로 Test-And-Set이라 불림
   - C 코드 일부를 사용한 TestAndSet 동작 정의
```c
1 int TestAndSet(int *old_ptr, int new) {
2     int old = *old_ptr; // old_ptr 이전 값 가져옴 
3     *old_ptr = new; // old_ptr에 new 값 저장
4     return old; // old 값 반환
5 }
```

2. TestAndSet 명령어가 하는 동작
   - ptr이 가리키고 있던 예전 값을 반환하여 동시에 new에 새로운 값 저장
   - 이 동작이 모두 원자적으로 수행된다는 것
   - Test-And-Set인 이유는 이전 값을 검사(Test)하는 (반환되는 값이며) 동시에 메모리에 새로운 값을 설정(Set)하기 때문임
   - Test-And-Set을 이용한 Spin-Lock 구현
<div align="center">
<img src="https://github.com/user-attachments/assets/4b619a00-51b8-4edb-bc10-7a0f34a4ea4c">
</div>

   - 처음 쓰레드가 lock()을 호출하고 다른 어떤 쓰레드도 현재 락을 보유하고 있지 않다고 가정
     + 현재의 flag의 값은 0이며, 이 쓰레드가 TestAndSet(flag, 1)을 호출하면 이 루틴은 flag의 이전 값인 0을 반환
     + flag 값을 검사한 쓰레드는 while문에서 회전하지 않고 락을 획득할 수 있게 됨
     + 이 쓰레드는 flag 값을 1로 설정하여 락을 보유하고 있음을 표시
     + 이 쓰레드가 임계 영역 내 동작을 끝마치면 unlock()을 호출하여 flag를 다시 0으로 변경

   - 처음 쓰레드가 락을 획득하여 flag 값이 1인 상태
     + 두 번쨰 쓰레드가 lock()을 호출하여 TestAndSet(flag, 1) 루틴을 실행
     + TestAndSet()는 이미 락을 다른 쓰레드가 보유하고 있으므로 예전 값으로 1을 반환하는 동시에 flag의 값을 다시 1로 설정
     + 락을 보유하고 있는 쓰레드가 있는 한 TestAndSet은 계속 1을 반환할 것임
     + 두 번째 쓰레드는 락이 해제될 때까지 계속 while문을 반복
     + 락을 보유하고 있는 쓰레드에 의해 flag 값이 0이 되면, 대기 중인 두 번째 쓰레드가 TestAndSet()를 호출하여 0을 받는 동시에 원자적으로 값을 1로 변경하고, 락을 획득했으므로 임계 영역 내 진입

3. 락의 값을 검사(Test)하고 새로운 값으로 설정(Set)하는 동작을 원자적 연산으로 만듦으로써 오직 하나의 쓰레드만 락을 획득할 수 있도록 함
4. 단일 프로세서에서 이 방식을 제대로 사용하려면 선점형 스케줄러(Preemptive Schedular)를 사용하여야 함
   - 선점형 스케줄러는 필요에 따라 다른 쓰레드가 실행될 수 있도록 타이머를 통해 쓰레드에 인터럽트를 발생시킬 수 있으며, 선점형이 아니면 단일 CPU에서 스핀 락 사용은 불가능한데, 이는 while문을 회전하며 대기하는 쓰레드가 CPU를 영원히 독점하기 떄문임
