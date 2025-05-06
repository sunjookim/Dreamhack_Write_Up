fsb_overwrite.c
```c
// Name: fsb_overwrite.c
// Compile: gcc -o fsb_overwrite fsb_overwrite.c

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void get_string(char *buf, size_t size) {
  /*
    ssize_t는 음수를 포함할 수 있는 정수형으로, 에러일 때 -1을 반환
    실제로 읽은 바이트 수를 i에 저장
    buf에 size만큼 입력받아 저장
  */
  ssize_t i = read(0, buf, size); 
  if (i == -1) {
    perror("read");
    exit(1);
  }
  /*
    입력이 버퍼 크기보다 작게 들어왔으면 (즉, 줄 끝이나 입력이 일찍 끝난 경우)
    마지막 문자가 \n이면 이를 제거하고 (i--),
    그 위치에 널 종료 문자(\0)를 넣어 문자열을 종료시킨다.
  */
  if (i < size) {
    if (i > 0 && buf[i - 1] == '\n') i--;
    buf[i] = 0;
  }
}

int changeme;

int main() {
  char buf[0x20]; // 32 바이트의 문자 배열 선언
  
  setbuf(stdout, NULL); // stdout 버퍼링을 비활성화 (출력 즉시 화면에 표시)
  
  while (1) {
    get_string(buf, 0x20); // 사용자로부터 최대 32바이트 입력을 받아 buf에 저장
    printf(buf); // 입력된 문자열을 포맷 문자열로 그대로 사용 → 포맷 스트링 버그 발생
    puts("");    // 줄 바꿈 출력
    if (changeme == 1337) {
      system("/bin/sh");
    }
  }
}

```
