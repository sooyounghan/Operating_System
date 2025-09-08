-----
### Pthread 락
-----
1. 쓰레드 간 상호 배제(Mutual Exclusion) 기능을 제공하므로 POSIX 라이브러리는 락을 Mutex라고 부름
2. 상호 배제는 한 쓰레드가 임계 영역 내에 있다면 이 쓰레드의 동작이 끝날 때까지 다른 쓰레드가 임계 영역에 들어올 수 없도록 제한한다고해서 얻은 이름
   - 래퍼를 사용해서 락과 언락 시 에러 확인하도록 추가 설정
```c
1 pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
2
3 Pthread_mutex_lock(&lock); // pthread_mutex_lock()을 위한 래퍼
4 balance = balance + 1;
5 Pthread_mutex_unlock(&lock);
```
  - POSIX 방식에서는 변수 명을 지정하여 락과 언락 함수에 전달 : 다른 변수를 보호하기 위해 다른 락을 사용할 수 있기 떄문임
  - 하나의 락이 임의의 임계 영역에 진입할 때 마다 사용하는 것이 아닌(거친(Coarse-Grained) 락 사용 전략), 서로 다른 데이터와 자료 구조를 보호하기 위해 여러 락을 사용하여 한 번에 여러 쓰레드가 서로 다른 락으로 보호된 코드 내에 각자가 진입이 가능하도록 할 수 있음(세밀한(Fine-Grained) 락 사용 전략)
