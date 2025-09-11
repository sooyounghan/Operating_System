-----
### 디렉토리 읽기
-----
1. ls : 디렉토리를 읽는 명령어
2. 디렉터리의 open은 파일을 open하는 것과 다른 새로운 시스템 콜
   - opendir(), readdir(), closedir()이 사용
   - 간단한 반복문을 사용하여 디렉토리 항목을 하나씩 읽은 후 디렉터리의 각 파일의 이름과 아이노드 출력
```c
1 int main(int argc , char *argv[]) {
2     DIR *dp = opendir(“.”);
3     assert(dp != NULL);
4     struct dirent *d;
5     while ((d = readdir(dp)) != NULL) {
6           printf(“%d %s\n ”, (int) d−>d_ino, d−>d_name);
7     }
8     closedir(dp);
9     return 0;
10 }
```

3. struct dirent 자료 구조 : 각 디렉토리 항목의 저장된 정보를 보여줌
```c
1 struct dirent {
2     char d_name[256];           /* filename */
3     ino_t d_ino;                /* inode number */
4     off_t d_off;                /* offset to the next dirent */
5     unsigned short d_reclen;    /* length of this record */
6     unsigned char d_type;       /* type of file */
7 };
```

4. 디렉토리에는 많은 정보가 있지 않으므로(단순하게 이름, 아이노드 번호 매핑 이 외 몇 가지만 제공) 프로그램은 각 파일에 stat()를 호출하여 파일 크기와 같은 구체적 정보를 얻음
5. ls -l를 전달받을 때, 추가 정보를 얻기 위해 stat()를 호출
