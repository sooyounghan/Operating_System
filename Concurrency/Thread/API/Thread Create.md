-----
### 쓰레드 생성 API
-----
```c
1 #include <pthread . h>
2 int
3 pthread_create(           pthread_t *        thread ,
4                   const   pthread_attr_t *   attr ,
5                           void *             (*start_routine)(void*) ,
6                           void *             arg);
```
1. thread, attr, start_routine, arg라는 4개의 인자 존재
2. thread는 pthread_t 타입 구조체를 가리키는 포인터
   - 이 구조가 쓰레드와 상호 작용 하는데 사용되므로 쓰레드 초기화 시 pthread_create()에 이 구조체를 전달

3. attr은 쓰레드 속성을 지정하는데 사용
   - 스택의 크기와 쓰레드 스케줄링 우선순위 같은 정보를 지정하기 위해 사용
   - 개별 속성은 pthread_attr_init() 함수를 호출하여 초기화
   - 대부분의 경우에는 디폴트 값을 지정하면 충분 (간단히 NULL 전달)

4. 세 번째 인자의 경우에는 쓰레드가 실행할 함수를 나타냄
   - C언어에서는 함수 포인터라 하고, 다음과 같은 정보가 필요하다고 알려줌
   - 이 함수는 void * 타입의 인자 한 개를 전달받음 (함수 이름은 start_routine)
   - 그리고 void * (void 포인터) 타입의 값 반환
   - void 포인터 타입 대신 integer를 인자로 사용하는 루틴이라면 다음과 같이 선언
```c
int pthread_create ( ... , // 처음 두 인자는 동일
                     void * (*start_routine)(int),
                     void * arg);
```

   - void 포인터 타입을 인자로 받지만, integer 타입을 반환한다면 다음과 같은 형식
```c
int pthread_create ( ... , // 처음 두 인자는 동일
                     int * (*start_routine)(void),
                     void * arg);
```

5. 마지막 네 번째 인자 arg는 실행할 함수에 전달할 인자를 나타냄
   - void 포인터를 start_routine 함수 인자로 사용하려면, 어떤 데이터 타입도 인자로 전달할 수 있고, 반환 값의 타입으로 사용하면 어떤 타입의 결과도 반환할 수 있음
   - 쓰레드 생성 코드 예
<div align="center">
<img src="https://github.com/user-attachments/assets/7bde7591-7ef1-40ec-98c6-96ce3f6b21f7">
</div>

   - 두 개의 인자를 전달받는 새로운 쓰레드를 생성하는 데 두 인자는 정의한 myarg_t 타입으로 묶여짐
   - 쓰레드가 생성되면 전달받은 인자의 타입을 예상하고 있는 타입으로 변환할 수 있고, 원하는 대로 인자를 풀 수 있음

6. 쓰레드를 생성하고 나면, 자신만의 스택까지 갖춘 프로그램의 실행 중인 모든 쓰레드와 같은 주소 공간에서 실행되는 또 하나의 실행 개체를 보유하게 됨
