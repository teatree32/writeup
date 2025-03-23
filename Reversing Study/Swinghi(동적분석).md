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

서브 함수인 ```hello``` 내부로 들어가보기 위해 우선 ```Ctrl+F2```로 디버깅을 중단하고 ```hello``` 함수에 중단점을 설정한다.

![image](https://github.com/user-attachments/assets/86211ddb-311a-454c-8a5f-97bf11425d68)

다음으로 디버깅을 다시 시작하고, ```F9```키를 눌러 ```hello```함수에 도달 한다.


![image](https://github.com/user-attachments/assets/88ec0e86-378c-41da-a5ba-f80861ab6a9e)


```F7``` 키를 눌러 함수 내부로 들어간다. 함수 내부로 ```RIP```가 이동한 것을 확인할 수 있다.

![image](https://github.com/user-attachments/assets/efb089a7-ff51-48bc-be9f-6a538e8f20bb)

Appendix, 실행 중인 프로세스 조작하기
--

기존 코드는 Sleep(delay=500)을 호출하여 0.5초 동안 프로세스를 정지 시켰다. 이번에는 ```delay```값을 10000으로 조작하여 프로세스를 정지시켜보겠다.

먼저 ```delay```를 ```Sleep``` 함수의 인자로 전달하는 부분에 중단점을 설정하고, 프로세스를 재시작한다.

![image](https://github.com/user-attachments/assets/cb251413-f498-47bd-a2c8-d452d481d22d)

스택을 보면 ```delay``` 값이 ```0x1f4```로 저장되어 있다. 

![image](https://github.com/user-attachments/assets/655462ec-84eb-430d-8c5d-43e205925cf2)

해당 값을 클릭하고 ```F2```키를 누른 뒤 ```0x2710(10000)```을 입력한다. 그리고 다시 ```F2```키를 눌러서 값을 저장하면 ```delay```값이 변경된 것을 확인 할 수 있다.

![image](https://github.com/user-attachments/assets/5b114eef-b024-473b-9d52-a5a3bc4ef1bc)

이제 다시 ```F9```를 눌러서 ```Sleep```함수를 호출하면 10초 대기 후에 프로세스가 재개되는 것을 알 수 있다.
