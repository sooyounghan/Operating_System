-----
### 락
-----
1. 락(Lock)을 통한 임계 영역에 대한 상호 배제 기법
```c
int pthread_mutex_lock(pthread_mutex_t *mutex);
int pthread_mutex_unlock(pthread_mutex_t *mutex);
```

2. 사용 예시
```c
pthread_mutex_t lock;
pthread_mutex_lock(&lock);
x = x + 1; // 또 다른 임계 영역의 코드를 사용할 수 있음
pthread_mutex_unlock(&lock);
```

   - pthread_mutex_lock()가 호출되었을 때 다른 어떤 쓰레드가 락을 가지고 있지 않다면 이쓰레드가 락을 얻어 임계 영역으로 진입
   - 만약 다른 쓰레드가 락을 가지고 있다면, 락 획득을 시도하는 쓰레드는 락을 얻을 때까지 호출에서 리턴하지 않음 (리턴했다면 락을 가지고 있던 쓰레드가 언락(Unlock)을 호출하여 락을 양도함을 의미)
   - 많은 쓰레드들이 락 획득 함수에서 대기 중일 수 있으므로, 락을 획득한 쓰레드만이 언락을 호출해야함

3. 하지만 이 코드는 두 가지 측면에서 정상적으로 작동하지 않음
   - 첫 번째 : 초기화를 하지 않음
     + 올바른 값을 가지고 시작했다는 것을 보장하기 위해 모든 락을 올바르게 초기화되어야 함
     + 그래야 락과 언락을 호출할 때 의도한대로 동작 가능
     + POSIX 쓰레드를 사용할 때 락을 초기화하는 방법은 두 가지
     + PTHREAD_MUTEX_INITIALIZER를 사용하는 방법과 pthread_mutex_init() 호출
```c
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;
```
   - 위 연산은 락을 디폴트 값으로 설정하는 것
   - 동적으로 초기화하는 방법 (실행 중에 초기화) 
```c
int rc = pthread_mutex_init(&lock , NULL);
assert(rc == 0); // 성공했는지 꼭 확인해야 함
```
   - 이 루틴의 첫번쨰 인자는 락 자체 주소이고, 두번째 인자는 선택 가능한 속성
     + NULL을 전달하면 디폴트 값을 사용

   - 정적, 동적 두 방법 모두 사용할 수 있지만 동적 방법을 주로 사용
   - 락 사용이 끝나면 초기화 API와 상응하는 pthread_mutex_destory()도 호출해야 함

   - 두 번째 문제 : 락과 언락을 호출할 때 에러 코드를 확인하지 않음
     + UNIX 시스템에서 호출하는 거의 모든 라이브러리 루틴과 마찬가지로 이 루틴들도 실패할 수 있음
     + 이 경우, 여러 쓰레드가 동시에 임계 영역에 들어갈 수 있으므로 최소한 래퍼 함수를 사용해 해당 루틴이 성공적으로 처리되었는지 확인해야 함
<div align="center">
<img src="https://github.com/user-attachments/assets/227b94cd-c86b-4166-a3f8-bb695c180713">
</div>

4. 락과 언락 루틴 외에 pthread 라이브러리에서 락 관련 루틴들이 더 존재
```c
int pthread_mutex_trylock(pthread_mutex_t *mutex);
int pthread_mutex_timedlock(pthread_mutex_t *mutex0, struct timespec *abs_timeout);
```
  - 이 두 함수는 락을 획득하는데 사용
  - trylock 버전은 락이 이미 사용 중이라면 실패 코드를 반환
  - timedlock 버전은 타임아웃이 끝나거나 락을 획득하거나의 두 조건 중 하나가 발생하면 리턴
    + 타임아웃을 0으로 설정하면 trylock과 동일하게 동작

  - 그러나 이 두 함수는 사용하지 않는 것이 좋음
  - 하지만 락 획득 루틴에서 무한정 대기하는 상황을 피하기 위해 사용되기도 함
