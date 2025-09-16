-----
### Remote Procedure Call (RPC)
-----
1. 프로그래밍 언어(PL) 차원의 개념에서의 분산 시스템으로 Remote Procedure Call (RPC)라고 불림
2. 목표
   - 원격 기계에서의 코드 실행을 로컬 내의 함수를 부르는 것처럼 간단하게 복잡하지 않게 만드는 것
   - 클라이언트는 프로시저 호출을 하고 잠시 후 결과를 리턴 받음
   - 서버는 공지할(Export) 루틴을 정의하며, 나머지는 RPC 시스템이 두 부분으로 나누어 담당 : 스텁 생성기(Stub Generator) 또는 프로토콜 컴파일러(Protocol Complier)와 런타임 라이브러리(Run-Time Library)
