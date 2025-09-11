-----
### 하드 링크
-----
1. 파일 삭제 시 unlink()를 사용하는 이유
   - 파일 시스템 트리에 항목을 추가하는 새로운 시스템 콜 link()
   - link() 시스템 콜의 인자
     + 하나는 원래의 경로명
     + 하나는 새로운 경로명

2. 원래 파일 이름에 새로운 이름을 연결(Link)하면 동일한 파일에 접근할 수 있는 새로운 방법 제공
   - 명령어 ln이 그 일을 함
```bash
prompt> echo hello > file
prompt> cat file
hello
prompt> ln file file2
prompt> cat file2
hello
```
  - hello라는 단어가 저장된 파일을 생성하고 이름을 file2로 설정
  - ln 프로그램을 사용해 이 파일의 하드 링크를 생성
  - 이후부터는 이 파일을 보려면 file 또는 file2를 열면 됨

3. link는 새로이 링크하려는 이름 항목을 디렉토리에 생성하고, 원래 파일과 같은 아이노드 번호를 가리킴 (파일이 복사되는 것이 아님)
   - 대신 같은 파일을 가리키는 두 개의 이름(file과 file2)이 생성
   - 각 파일의 아이노드 번호 출력
```bash
prompt> ls −i file file2
67158084 file
67158084 file2
prompt>
```
   - ls -i 명령어를 사용하면 각 파일의 아이노드 번호(파일 이름과 함께) 출력
   - 동일한 아이노드 번호(여기서는 67158084)에 대한 새로운 링크 생성

4. unlink()
   - 파일을 생성할 때 두 가지 작업을 실시
     + 파일 관련 대부분 정보를 관리하는 자료구조(아이노드) 생성 (파일 크기와 디스크 블럭의 위치 등 포함)
     + 해당 파일에 사람이 읽을 수 있는 이름을 연결하고 그 연결 정보를 디렉토리에 생성
   - 파일 시스템에서 파일의 하드 링크를 생성 후 원래 파일 이름(file)과 새로 생성된 파일 이름(file2) 간에는 차이가 존재 : 두 개의 파일 이름은 번호 67158084에서 찾을 수 있는 파일의 메타데이터에 대한 연결일 뿐임
   - 파일 삭제 시 unlink()를 호출
```bash
prompt> rm file
removed `file '
prompt> cat file2
hello
```
   - 파일을 unlink()하면 아이노드 번호의 참조 횟수(Reference Count)를 검사
   - 이 참조 횟수(= 연결 횟수(Link Count))가 특정 아이노드에 대해 다른 이름이 몇 개나 연결되었는지 관리하며, unlink()가 호출되면 이름과 해당 아이노드 번호 간의 연결을 끊고 참조 횟수를 하나 줄임
   - 참조 횟수가 0에 도달하면 파일 시스템은 비로소 아이노드와 관련된 데이터 블럭을 해제하여 파일을 진정으로 삭제

5. 파일의 참조 횟수는 stat()를 사용해 확인 가능
   - 파일에 대한 하드 링크를 생성과 삭제할 때 얼마인지 확인
```bash
prompt> echo hello > file

prompt> stat file
... Inode: 67158084 Links: 1 ...

prompt> ln file file2

prompt> stat file
... Inode: 67158084 Links: 2 ...

prompt> stat file2
... Inode: 67158084 Links: 2 ...

prompt> ln file2 file3

prompt> stat file
... Inode: 67158084 Links: 3 ...

prompt> rm file
prompt> stat file2
... Inode: 67158084 Links: 2 ...

prompt> rm file2
prompt> stat file3
... Inode: 67158084 Links: 1 ...

prompt> rm file3
```
