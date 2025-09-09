-----
### 컨디션 변수
-----
1. 쓰레드가 계속 진행하기 전에 어떤 조건이 참인지를 검사해야 하는 경우가 많음
   - 예를 들어, 부모 쓰레드가 작업을 시작하기 전 자식 쓰레드가 작업을 끝냈는지 검사하기를 원할 수 있음(= join())
<div align="center">
<img src="https://github.com/user-attachments/assets/3d876969-2d80-4a49-8498-ca9356876846">
</div>

   - 다음과 같은 출력문을 원함
```
parent: begin
child
parent: end
```

2. 공유 변수를 사용할 수 있음
<div align="center">
<img src="https://github.com/user-attachments/assets/8812009e-a3d3-4cc4-94e9-9d3c3494c063">
</div>

   - 이 방법은 제대로 동작하지만 부모 쓰레드가 회전을 하면서 CPU 시간을 낭비하므로 비효율적

3. 이 방법 대신 부모 쓰레드가 특정 조건이 참이 될 때까지(예) 자식 쓰레드가 실행이 종료되는 것) 잠자면서 기다리는 방법이 더 좋음
   
