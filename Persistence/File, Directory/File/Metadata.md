-----
### 파일 정보 추출
-----
1. 파일 시스템은 각 파일에 대한 정보를 보관
2. 메타데이터(Metadata) : 파일에 대한 정보
3. 어떤 파일의 메타데이터를 보려면 stat()이나 fstat() 시스템 콜을 사용
   - 이 호출들은 파일에 대한 경로명(또는 파일 디스크럽터)을 입력으로 받음
   - stat의 구조
<div align="center">
<img src="https://github.com/user-attachments/assets/6021b8f4-6c75-4dda-a4d2-7f83c207d723">
</div>

   - 각 파일에 관한 많은 정보가 있다는 것을 알 수 있음
   - 파일의 크기(바이트 단위), 저수준 이름(아이노드 번호), 소유권, 파일이 접근되고 변경된 시간, 그 외에도 많은 정보가 있음
   - 정보를 확인하기 위해 stat 사용
```bash
prompt> echo hello > file
prompt> stat file
    File: `file '
    Size: 6 Blocks: 8 IO Block: 4096 regular file
  Device: 811h/2065d Inode: 67158084 Links: 1
  Access: (0640/−rw−r−−−−−) Uid: (30686/ remzi) Gid: (30686/ remzi)
  Access: 2011−05−03 15:50:20.157594748 −0500
  Modify: 2011−05−03 15:50:20.157594748 −0500
  Change: 2011−05−03 15:50:20.157594748 −0500
```
  - 일반적으로 파일 시스템은 아이노드(Dnode라고도 함)에 이 정보를 보관
    
