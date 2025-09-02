-----
### 선입선출 (First In First Out)
-----
1. 가장 기초적인 스케줄링 알고리즘으로, 선도착선처리(First Come First Served, FCFS) 스케줄링이라고도 알려져 있음
2. 단순하고 구현하기 쉬우며, 설정한 기본 가정 하에서 잘 동작
3. 간단한 예) 시스템에 3개의 작업 A, B, C가 거의 동시에 도착했다고 가정($T_{arrival} = 0$)
   - 간발의 차이로 A, B, C 순서대로 도착했다고 가정하며, 각 작업은 10초 동안 실행된다고 가정
<div align="center">
<img src="https://github.com/user-attachments/assets/b2e02902-777c-4d9c-8557-c1376c925e6f">
</div>

   - 평균 반환 시간 : A는 10, B는 20, C는 30에 종료했으므로, 세 작업의 평균 반환 시간은 (10 + 20 + 30) / 3 = 20

4. 실행 시간이 모두 같지 않다고 가정
<div align="center">
<img src="https://github.com/user-attachments/assets/71e10e80-e259-407f-916b-20a1cb710ccd">
</div>

   - 구체적으로 A, B, C 세 개의 작업을 다시 가정하되, A는 100초, B와 C는 10초 동안 실행
   - 작업 A가 B와 C보다 먼저 100초 동안 실행되므로, 시스템 평균 반환 시간은 (100 + 110 + 120) / 3 = 110으로 늘어남
   - 이 문제점은 Convey Effect로 칭해지며, 짧은 시간 동안 자원을 사용할 프로세스들이 자원을 오랫동안 사용하는 프로세스 종료를 기다리는 현상을 말함
 
