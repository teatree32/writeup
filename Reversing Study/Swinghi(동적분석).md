<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] Swinghi.exe 동적분석</h1>
</body>
<br>
<br>
</html>

main 함수 진입
---

```F2``` 키를 눌러 main 함수에 중단점을 설정해보자.

![image](https://github.com/user-attachments/assets/6a7965b4-2108-44e6-ba4e-b84c126cfcd9)

다음으로 ```F9``` 키를 눌러 디버깅을 시작한다.

![image](https://github.com/user-attachments/assets/fcb4ce71-180d-48e9-861f-68713c9de089)


한 단계 실행(Step Over)
--

```F8``` 키를 누르면서 프로그램의 동작을 분석해 보자.

1. push rbp -> 기존 ```rbp```값을 스택에 저장
2. move rbp, rsp -> ```rsp```를 ```rbp```로 설정
3. sub rsp, 30h -> 스택 공간을 0x30(48byte) 확보
4. call __main -> main 함수 호출
5. mov [rbp+dwMilliseconds], 1F4h -> ```rbp+dwMilliseconds```에 ```0x000001f4```를 저장
6. mov eax, [rbp+dwMilliseconds] -> ```[rbp+dwMilliseconds]```에 저장된 값을 ```eax```로 옮김
7. mov ecx, eax -> ```eax```에 값을 ```ecx```로 옮김
8. mov rax,cs:__imp_Sleep -> ```Sleep```함수를 가리킴
9. call rax -> ```Sleep``` 함수 호출, ```ecx```가 ```0x1f4``` 이므로 ```Sleep(500)```이 실행되어 0.5초간 실행이 멈춤
10. call hello -> ```hello``` 함수 실행, 메시지 박스 출력
    
![image](https://github.com/user-attachments/assets/ea71316d-6cdd-4c02-b91b-a80affdba60d)

11. call cal -> ```cal``` 함수 실행, 메세지 박스 출력

![image](https://github.com/user-attachments/assets/8dd102b5-f937-4e67-9acd-db374084cb86)

12. mov eax, 0 -> ```eax = 0``` 반환값 0 설정
13. add rsp, 30h -> ```rsp```를 ```0x30``` 증가, 스택 정리(함수 시작 시 ```sub rsp, 30h```로 확보했던 공간 복원
14. pop rbp -> ```rbp``` 값 복원, 이전 스택 프레임으로 복귀
15. retn -> 함수 종료

함수 내부 진입하기(Step Into)
--

```Ctrl+F2```로 디버깅을 중단하고 ```hello``` 함수에 중단점을 설정한 후, ```F7```키로 해당 함수 내부로 진입해 보자.




