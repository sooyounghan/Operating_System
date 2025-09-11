-----
### 심볼릭 링크(Symbolic Link)
-----
1. 소프트 링크(Soft Link)라고도 불림
2. 하드 링크는 제한이 많은 편
   - 디렉터리에 대해서 하드 링크를 만들 수 없음 (디렉토리 트리에 순환 구조 발생 우려)
   - 다른 디스크 파티션에 있는 파일에 대해서도 하드 링크를 걸 수 없음 (아이노드 번호는 하나의 파일 시스템 내에서만 유일함) 등의 제한 존재

3. 심볼링 링크를 만들기 위해서는 동일한 ln 프로그램 사용하되, -s 플래그 필요
```bash
prompt> echo hello > file
prompt> ln −s file file2
prompt> cat file2
hello
```
  - 원래의 파일은 기존의 이름인 file뿐만 아니라 file2라는 심볼릭 링크 이름으로도 접근 가능

4. 표면적으로는 유사하지만, 매우 다름
   - 첫 번째 차이는 심볼릭 링크는 다른 형식의 독립된 파일 (즉, 파일 시스템에 존재하는 세 번쨰 종류 유형)
   - 심볼릭 링크에 대해 stat()
```bash
prompt> stat file
... regular file ...
prompt> stat file2
... symbolic link ...
```

5. ls를 실행하여도 확인 가능
```bash
drwxr−x−−− 2 remzi remzi 29 May 3 19:10 ./
drwxr−x−−− 27 remzi remzi 4096 May 3 15:14 ../
−rw−r−−−−− 1 remzi remzi 6 May 3 19:10 file
lrwxrwxrwx 1 remzi remzi 4 May 3 19:10 file2 −> file
```
  - ls의 긴 형식의 출력의 첫 글자를 살펴보면 가장 왼쪽 열의 글자가 일반 파일에 대해서는 -로 표시, 디렉토리는 d로, 소프트 링크는 l로 표시
  - 또한, 심볼릭 링크의 크기는(이 경우 4바이트)를 확인할 수 있고, 연결의 대상도 보여줌 (file이라는 이름을 갖는 파일)
  - file2의 크기가 4바이트인 이유 : 심볼릭 링크는 연결하는 파일의 경로명을 저장
    + file이라는 파일에 연결했기에 연결된 파일의 file2 크기는 작음 (4바이트)
    + 좀 더 긴 경로명에 연결하면 심볼릭 링크 파일은 좀 더 길어짐

```bash
prompt> echo hello > alongerfilename
prompt> ln −s alongerfilename file3
prompt> ls −al alongerfilename file3
−rw−r−−−−− 1 remzi remzi 6 May 3 19:17 alongerfilename
lrwxrwxrwx 1 remzi remzi 15 May 3 19:17 file3 −> alongerfilename
```

6. 심볼링 링크의 만들어지는 방식에 의해 Dangling Reference 문제 발생
```bash
prompt> echo hello > file
prompt> ln −s file file2
prompt> cat file2
hello
prompt> rm file
prompt> cat file2
cat: file2: No such file or directory
```
   - 하드 링크와 매우 다르게 원래 파일인 file를 삭제하면 심볼릭 링크가 가리키는 실제 파일은 더 이상 존재하지 않음
