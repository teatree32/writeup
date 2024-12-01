<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>AlpacaHack Round 6 #Pwn echo Write-Up</h1>
</body>
<br>
<br>
</html>

문제파일을 다운로드 받아 실행해 보았다.

아래 사진과 같이 Size와 Data 값을 입력하면 입력한 값을 Size에 입력한 값을 넘기지 않고 출력한다.

![image](https://github.com/user-attachments/assets/85f6dccd-8acc-4c5b-98f1-9b36189d2779)

이어서 main.c 코드를 살펴 보았다.
```python
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#define BUF_SIZE 0x100

/* Call this function! */
void win() {
  char *args[] = {"/bin/cat", "/flag.txt", NULL};
  execve(args[0], args, NULL);
  exit(1);
}

int get_size() {
  // Input size
  int size = 0;
  scanf("%d%*c", &size);

  // Validate size
  if ((size = abs(size)) > BUF_SIZE) {
    puts("[-] Invalid size");
    exit(1);
  }

  return size;
}

void get_data(char *buf, unsigned size) {
  unsigned i;
  char c;

  // Input data until newline
  for (i = 0; i < size; i++) {
    if (fread(&c, 1, 1, stdin) != 1) break;
    if (c == '\n') break;
    buf[i] = c;
  }
  buf[i] = '\0';
}

void echo() {
  int size;
  char buf[BUF_SIZE];

  // Input size
  printf("Size: ");
  size = get_size();

  // Input data
  printf("Data: ");
  get_data(buf, size);

  // Show data
  printf("Received: %s\n", buf);
}

int main() {
  setbuf(stdin, NULL);
  setbuf(stdout, NULL);
  echo();
  return 0;
}
```

