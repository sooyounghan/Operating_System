-----
### 서버 측 쓰기 버퍼링의 의미
-----
1. NFS 서버들도 대용량 메모리를 갖고 있으므로 그 자체도 캐싱 문제를 가짐
   - 디스크에서 데이터를(메타데이터와) 읽으면, NFS 서버는 그것을 메모리에 보관
   - 다음 번 해당 데이터(그리고 메타데이터)에 대한 읽기는 디스크에서 서비스되지 않음
   - 캐싱으로 인해 잠재적인(약간의) 성능이 개선

2. 쓰기 버퍼링의 경우, NFS 서버는 WRITE 프로토콜 요청을 받으면 저장 장치(예) 디스크 또는 다른 영속 저장 장치)에 완전히 쓴 후 리턴
   - 데이터를 서버 메모리에만 저장한 후 클라이언트에 알릴 수 있지만 잘못된 결과 초래 가능
   - 이유 : 클라이언트가 서버 측 실패를 다루는 방법에 대한 가정 떄문임
     + 클라이언트가 다음과 같은 쓰기 문장들을 실행했다고 가정
```c
write(fd , a_buffer , size); // 첫 번째 블럭을 a로 채움
write(fd , b_buffer , size); // 두 번째 블럭을 b로 채움
write(fd , c_buffer , size); // 세 번째 블럭을 c로 채움
```

   - 이 쓰기들은 파일의 첫 블럭을 a로 채우고, 다음은 b로 채우고, 그 다음을 c로만 덮어씀
   - 처음의 파일 상태
```
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy
zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz
```

   - 세 번의 쓰기 수행 후 최종적인 모습 (원래 x, y, z가 a, b, c로 덮어씌어짐)
```
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb
cccccccccccccccccccccccccccccccccccccccccccccccccccccccccccc
```

   - 클라이언트가 세 개의 쓰기 요청을 세 개의 쓰기 프로토콜로 전송했다고 가정
   - 첫 번쨰 WRITE 메세지를 서버가 디스크로 요청했으며, 클라이언트가 성공했다는 답신을 받음
   - 이제 두 번쨰 쓰기 내용을 메모리 버퍼에 저장했고, 해당 내용을 디스크에 저장하기 전에 성공했다고 클라이언트에게 답신
   - 불행하게도, 디스크에 두 번쨰 요청 내용이 저장되기 전 시스템이 크래시되었고, 서버는 신속히 재시작하여 세 번째 요청을 받아 성공적 처리 되었음
   - 클라이언트는 모든 요청이 성공적으로 처리된 것으로 알았는데, 파일의 내용은 다음과 같음
```
aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy <− 문제 발생
cccccccccccccccccccccccccccccccccccccccccccccccccccccccccccc
```
   - 서버가 두 번쨰 쓰기 내용을 디스크에 완전히 저장하기 전 디스크에 예전 내용이 있는 상황에서 클라이언트에게 답신을 보내므로 문제 발생

3. 문제를 피하기 위해 NFS 서버는 클라이언트에게 성공했다고 알리기 전 반드시 각 쓰기를 안정적인(영속적인) 저장 장치에 커밋해야 함
   - 그렇게 해야 클라이언트가 쓰기 도중 서버가 실패하는 경우를 파악할 수 있으며 성공할 때까지 재시도할 수 있음
   - 즉, 이전 내용과 최신 내용이 무질서하게 섞여있는 경우 방지 가능

4. 이러한 요구 조건을 만족시키도록 NFS 서버를 구현하면 쓰기 성능에 문제 발생
   - 세심하게 다루지 않으면 중대한 성능 병목의 원인이 될 수 있음
   - 어떤 회사들은 쓰기를 빠르게 할 수 있는 NFS 서버를 간단히 구현
     + 메모리에 배터리를 연결하여 데이터를 디스크에 기록하지 않고도 손실 위험없이 빠르게 WRITE 요청에 응답하도록 함
     + 쓰기 성능에 최적화된 파일 시스템 개발
