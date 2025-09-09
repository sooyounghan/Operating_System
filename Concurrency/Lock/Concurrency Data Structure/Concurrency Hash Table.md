-----
### 병행 해시테이블
-----
1. 확장이 되지 않는 간단한 해시 테이블 (많이 사용되는 병행 자료 구조 중 하나 : 해시 테이블)
<div align="center">
<img src="https://github.com/user-attachments/assets/29d4ad1c-2126-4655-ad4f-28963ca43932">
</div>

2. 성능이 우수 : 전체 자료 구조에 하나의 락을 사용한 것이 아닌 해시 버켓(리스트로 구현)마다 락을 사용 : 병행성이 좋아짐

3. 4개의 CPU를 갖는 iMac에서 4개의 쓰레드가 해시 테이블에 각각 10,000개에서 50,000개의 갱신 연산을 수행할 때 성능
<div align="center">
<img src="https://github.com/user-attachments/assets/02ad7842-67d8-418e-9dde-c6cb96bdfddc">
</div>

   - 비교를 위해 단일 락을 사용하는 연결 리스트 성능도 함께 보임
   - 병행 해시 테이블은 확장성이 매우 좋으며, 연결 리스트는 확장성이 매우 떨어짐
