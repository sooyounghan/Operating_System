-----
### Compare-And-Swap
-----
1. 또 다른 하드웨어 기법은 SPARC의 Compare-And-Swap, x86에서는 Comapre-And-Exchange가 있음
2. C로 작성된 명령어 의사 코드
<div align="center">
<img src="https://github.com/user-attachments/assets/ff720118-47a8-4a49-b983-1ee6390c8e44">
</div>

   - ptr이 가리키고 있는 주소의 값을 expected 변수와 일치하는지 검사
   - 만약 일치한다면, ptr이 가리키는 주소의 값을 새로운 값으로 변경
   - 불일치한다면, 아무것도 하지 않음
   - 원래의 메모리 값을 반환하여 CompareAndSwap을 호출한 코드가 락 획득 성공 여부를 알 수 있도록 함

2. lock() 루틴
<div align="center">
<img src="https://github.com/user-attachments/assets/9751bfef-0e1c-4a49-8793-cb1579cc5616">
</div>

   - flag 변수가 0인지 검사하고 만약 그렇다면 자동적으로 1로 바꾸어 락을 획득
   - 다른 쓰레드가 락을 보유하고 있는 중에 락을 획득하려고 시도하는 쓰레드는 락이 획득 가능한 상태가 될 때까지 while문에서 회전

3. x86 기반에서 C에서 호출 가능한 형태의 Compare-And-Swap 방식
<div align="center">
<img src="https://github.com/user-attachments/assets/2efccc07-e22b-4e8c-b6a3-770dc6de67d5">
</div>

4. Compare-And-Swap 명령어는 Test-And-Set 명령어보다 더 강력
