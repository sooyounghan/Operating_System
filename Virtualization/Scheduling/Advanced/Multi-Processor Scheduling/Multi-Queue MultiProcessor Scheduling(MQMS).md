-----
### 멀티 큐 스케줄링 (Multi-Queue MultiProcessor Scheduling(MQMS))
-----
1. 멀티 큐, 예를 들어 CPU마다 큐를 하나씩 두는 스케줄링
2. 기본적인 스케줄링 프레임워크는 여러 개 스케줄링 큐로 구성
   - 각 큐는 라운드 로빈과 같은 특정 스케줄링 규칙을 따를 것이고, 어떤 스케줄링 기법도 사용이 가능
3. 작업이 시스템에 들어가면 하나의 스케줄링 큐에 배치
   - 배치될 큐의 결정은 적당한 방법을 따름 (예를 들어, 무작위로 할 수 있고, 다른 큐보다 작은 수의 작업이 있는 큐로 배치 가능)
   - 이후 각 독립적으로 스케줄되므로 단일 큐 방식에서 보았던 공유 및 동기화 문제를 피할 수 있음

4. 예) 2개의 CPU(CPU 0과 CPU 1)와 A, B, C, D 네 개의 작업이 시스템에 존재한다고 가정
   - CPU는 각자 하나씩 스케줄링 큐를 가지고 있으므로 운영체제는 각 작업을 배치할 큐를 결정해야 함
<div align="center">
<img src="https://github.com/user-attachments/assets/9e13c77f-6bfe-4e06-859e-45db94d33a0d">
</div>

   - 큐 스케줄링 정책에 따라 각 CPU는 현재 2개의 작업을 가지고 있으며, 예를 들어 라운드 로빈의 경우 다음과 같이 스케줄 생성
<div align="center">
<img src="https://github.com/user-attachments/assets/a5173691-6915-4f0a-b038-fa7600798fab">
</div>

5. MQMS가 SQMS에 비해 가지는 명확한 이점은 확장성이 좋음
   - CPU 개수가 증가할수록, 큐의 개수도 증가하므로 락과 캐시 경합(Cache Contention)은 더 이상 문제가 되지 않음

6. MQMS는 본질적으로 캐시 친화적임
   - 작업이 같은 CPU에서 계속 실행되므로 캐시에 저장된 내용을 재사용하는 이점을 얻음
  
7. 문제점 : 워크로드의 불균형(Load Imbalance)
   - 4개의 작업, 2개의 CPU를 가지며, 그 중 하나(예) C)가 종료되었다고 가정한 뒤, 다음과 같은 스케줄링 큐를 가짐
<div align="center">
<img src="https://github.com/user-attachments/assets/f8eb0231-a2da-432e-acad-2fcdd4d4995f">
</div>

   - 라운드 로빈을 각 큐에 정책으로 실행할 경우, 다음과 같은 스케줄을 얻을 수 있음
<div align="center">
<img src="https://github.com/user-attachments/assets/08c91aa6-815e-4749-8be5-f849b65bb1bc">
</div>

   - A가 B와 D보다 2배의 CPU를 차지하고 있는 결과가 나옴
   - A와 C가 모두 종료하여 B와 D가 남았다고 가정
<div align="center">
<img src="https://github.com/user-attachments/assets/037b724c-822e-4ead-be30-5aea45e68922">
</div>

   - CPU 0은 유휴 상태가 되며, CPU 사용 타임라인은 다음과 같이 됨
<div align="center">
<img src="https://github.com/user-attachments/assets/05279a92-7299-4002-8837-ba1b83254e8c">
</div>

   - 해결책 : 이주(Migration), 즉 작업을 이리저리로 이동시키는 것으로, 작업을 한 CPU에서 다른 CPU로 이주시킴으로써 워크로드 균형을 달성
      + 하나의 CPU는 유휴 상태이며, 다른 CPU는 몇몇 작업을 가지고 있다고 가정
<div align="center">
<img src="https://github.com/user-attachments/assets/319c9a84-c7bb-469e-b8c2-e1f9ae8b78d4">
</div>

   - 이 경우 운영체제 B 또는 D 중 하나를 CPU 0로 이동시키며, 단 한 번의 작업 이주로 균형을 이루게 됨
   - 또 다른 예시 : A는 혼자 CPU 0에 있었고, B와 D는 CPU 1에서 번갈아 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/6b1341ca-5952-4980-a510-bc284a34b557">
</div>

   - 이 경우 한 번의 이주만으로는 문제가 해결되지 않음
     + 작업들을 지속적으로 이주시켜야 함
     + 즉, 처음에 A는 CPU 0에 혼자 있고, B와 D는 CPU 1에서 번갈아 실행 중이고, 몇 번의 타임 슬라이스 후 B는 CPU 0로 이주하여 A와 경쟁하고, 그동안 D는 CPU 1에서 몇 개의 타임 슬라이스 동안 혼자 실행하여 워크로드의 균형을 맞춤
<div align="center">
<img src="https://github.com/user-attachments/assets/f7c6cd07-c4d0-470b-a810-5eca2966a536">
</div>

   - 물론, 다양한 가능한 많은 이주 패턴이 존재
     + 한 가지 기본적인 접근 방식은 작업 훔치기(Work Stealing)이라는 기술로, 작업의 개수가 낮은 (소스) 큐가 가끔 다른 (대상) 큐에 훨씬 많은 수의 작업이 있는지 검사하여, 대상 큐가 소스 큐보다 더 가득 차있다면, 워크로드 균형을 위해 소스는 대상에서 하나 이상의 작업을 가져옴
     + 하지만, 큐를 너무 자주 검사하게 되면 높은 오버헤드로 확장성 문제가 생기며, 확장성은 멀티 큐 스케줄링의 가장 중요한 목적임
     + 하지만, 다른 큐를 자주 검사하지 않으면, 심각한 워크로드 불균형을 초래할 가능성이 있으므로, 적절한 값을 찾아내야함
