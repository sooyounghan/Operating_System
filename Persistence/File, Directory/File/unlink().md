-----
### 파일 삭제
-----
1. UNIX에서는 rm 명령어를 실행하면 파일 삭제
2. rm에서 사용되는 시스템 콜 (strace를 통해 확인)
  - foo 파일 삭제
```bash
prompt> strace rm foo
...
unlink(“foo”) = 0
...
```
  - unlink()라는 시스템 콜이 사용

3. unlink()는 지워야 하는 파일 이름을 인자로 받은 후 성공하면 0을 리턴
