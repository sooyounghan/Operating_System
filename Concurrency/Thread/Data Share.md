-----
### 데이터의 공유
-----
1. 쓰레드가 공유 데이터를 접근하기 위해 상호작용 하는 과정
   - 전역 공유 변수를 갱신하는 두 개의 쓰레드에 관한 간단한 예제
<div align="center">
<img src="https://github.com/user-attachments/assets/4adb1452-a384-4f45-8576-1a7e8252dab5">
</div>

   - 쓰레드를 생성하는 루틴과 조인하는 루틴이 실패할 경우 간단하게 종료하도록 래퍼 함수 생성
     + Pthread_create()는 단순히 pthread_create()를 호출하고 리턴 값이 0인지 확인하며, 0이 아니면 Pthread_create()는 에러 메세지를 출력하고 종료
   - 작업자 쓰레드를 위해 두 개의 독립된 함수를 구성하는 대신 하나의 단일 코드를 사용 : 메세지를 출력하기 전 각 쓰레드가 다른 문자를 출력하도록 쓰레드에게 문자열 인자 전달
   - 각 작업자가 무엇을 하려는지 알 수 있음 : 각 작업자 쓰레드는 반복문 안에서 공유 변수인 counter에 수를 천 만번 (1e7) 더하므로, 결과적으로 최종적으로 얻으려는 값은 20,000,000

   - 실행 결과
```
1 prompt> ./main
2 main: begin (counter = 0)
3 A: begin
4 B: begin
5 A: done
6 B: done
7 main: done with both (counter = 20000000)
```

2. 하지만 단일 프로세서라고 하더라도 기대한 결과대로 출력되지 않을 수 있음
```
1 prompt> ./main
2 main: begin (counter = 0)
3 A: begin
4 B: begin
5 A: done
6 B: done
7 main: done with both (counter = 19345221)
```

```
1 prompt> ./main
2 main: begin (counter = 0)
3 A: begin
4 B: begin
5 A: done
6 B: done
7 main: done with both (counter = 19221041)
```
