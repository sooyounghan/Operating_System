-----
### Lock-Linked, Store-Conditional
-----
1. 어떤 플랫폼은 임계 영역 진입 제어 함수를 제작하기 위한 명령어 쌍을 제공
   - MIPS 구조에서는 Load-Linked와 Store-Conditional 명령어를 앞 뒤로 사용하여 락이나 기타 병행 연산을 위한 자료 구조를 만들 수 있음
   - Alpha, PowerPC, ARM에서도 유사한 명령어 지원
<div align="center">
<img src="https://github.com/user-attachments/assets/eddc37cb-87ab-43ab-ba98-35f242206342">
</div>

2. load-linked는 일반 로드 명령어와 같이 메모리 값을 레지스터에 저장
3. 실제 차이는 store-conditional 명령어에서 나타남
   - store-conditional 명령어는 동일한 주소에 다른 스토오가 없었던 경우에만 저장을 성공
   - 저장이 성공하면, load-linked가 탑재했던 값을 갱신
   - 성공한 경우에는 store-conditional은 1을 반환하고, ptr이 가리키는 value의 값을 갱신
   - 실패한 경우에는 ptr이 가리키는 value 값이 갱신되지 않고 0을 반환

4. load-linked와 store-conditional을 사용하여 락을 만드는 방법
<div align="center">
<img src="https://github.com/user-attachments/assets/aed6cd85-3fbe-4f3d-9902-526dff1a3153">
</div>

   - 쓰레드가 while문을 돌며 flag가 0이 되기를 기다림 (락이 해제된 상태)
   - 락이 획득 가능한 상태가 되면 쓰레드는 store-conditional 명령어로 락 획득을 시도하고 만약 성공한다면 flag의 값을 1로 변경하여 임계 영역 내로 진입

5. store-conditional 명령어가 실패할 수 있음
   - 쓰레드가 lock()을 호출하여 load-linked를 실행하고 락이 사용한 가능한 상태이므로 0을 반환
   - store-conditional 명령어를 시도하기 전 이 쓰레드는 인터럽트에 걸렸고, 다른 쓰레드가 락 코드를 호출하고 load-linked 명령어는 실행하여 0을 반환 받은 후 계속 진행한다고 가정
   - 이 시점에 두 쓰레드 모두 load-linked 명령어를 실행하였고, store-conditional을 부르려는 상황
     + 이 명령어의 주요 기능은 오직 하나의 쓰레드만 flag 값을 1로 설정 후 락을 획득할 수 있도록 하는 것
     + store-conditional을 두 번쨰로 실행하는 쓰레드는 락 획득을 실패하고(load-linked와 store-conditional 명령어 사이에 다른 쓰레드가 flag 값을 갱신하였기 때문임), 다음 기회를 기다려야 함
```c
1 void lock(lock_t *lock) {
2 while (LoadLinked(&lock−>flag) || !StoreConditional(&lock−>flag, 1))
3     ; // 회전
4 }
```
