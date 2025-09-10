-----
### 평균 탐색 시간 계산
-----
1. 평균 디스크 탐색 시간 : 전체 탐색 시간의 대략 $\frac{1}{3}$ 정도로 설정
2. 평균 탐색 시간이 아니라 평균 탐색 거리를 기준으로 간단한 계산을 해보면 확인 가능
   - 트랙이 0부터 N개가 있는 디스크를 가정
   - 두 개의 트랙 $x$와 $y$ 간의 탐색 거리는 $\lvert x - y \rvert$로 $x$와 $y$의 차의 절대치
  
3. 평균 탐색 거리를 계산을 위해 할 일은 가능한 모든 탐색 거리를 다 더함
<div align="center">
<img src="https://github.com/user-attachments/assets/7a2a8efa-0f53-4c4d-a900-a3658e1b9a18">
</div>

4. 모든 가능한 서로 다른 탐색들의 수인 $N^{2}$로 나누는데, 다 더하기 위해 적분식 사용
<div align="center">
<img src="https://github.com/user-attachments/assets/cb783464-9f68-4f50-a001-55b44fe55a50">
</div>

5. 안쪽의 적분을 위해 절대치를 다음과 같이 두 구간으로 나눔
<div align="center">
<img src="https://github.com/user-attachments/assets/672081e5-19ea-4834-9ddf-d76b69b18150">
</div>

6. 이 식을 계산하면 $(xy - \tfrac{1}{2}y^2)\Big|_0^x + \left(\tfrac{1}{2}y^2 - xy\right)\Big|_x^N$을 얻을 수 있으며, 간단하게 만들면, $(x^2 - Nx + \tfrac{1}{2}N^2)$을 얻음
7. 바깥쪽 적분 실행
<div align="center">
<img src="https://github.com/user-attachments/assets/83ae8be7-0ae7-49c3-9b19-9faf74caca18">
</div>

8. 계산하면 다음과 같은 식을 얻음
<div align="center">
<img src="https://github.com/user-attachments/assets/62b2584b-105b-41a9-bc70-4c50cdb3923b">
</div>

9. 전체 탐색 횟수($N^{2}$)로 위의 식을 나눠야 평균 탐색 거리를 얻을 수 있음
10. 따라서 계산하면 ($\frac{N^3}{3}$) / ($N^{2}$) = $\frac{1}{3} N$ 을 얻음
11. 그러므로 디스크의 평균 탐색 거리는 모든 가능한 탐색을 따져보았을 때, 전체 거리의 1/3이 됨
