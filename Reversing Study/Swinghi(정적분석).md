<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] Swinghi.exe 정적분석</h1>
</body>
<br>
<br>
</html>

문자열 검색
-------------
swinghi.exe 파일을 IDA로 열어보았다.

```shift+F12```키를 눌러 바이너리에 포함된 문자열이 열거된 String 창을 확인한다.

![image](https://github.com/user-attachments/assets/884ef280-59fe-421c-b671-1db788e1dc2d)

이때 "Hello, swing!"이라는 문자열은 컴파일 과정에서 삽인된 문자열이 아니라 프로그래머가 추가한 문자열 같다.

해당 문장열을 더블 클릭하여 따라가보자.

![image](https://github.com/user-attachments/assets/a03780f8-4611-4b41-89dd-6989d4b70a6a)

상호 참조
-------
"aHelloswing"을 클릭한 상태로 상호 참조 단축키 ```x```를 누른다.

![image](https://github.com/user-attachments/assets/89edd0c2-1fb4-4732-a49c-12fc96540630)

그러면 위와 같이 문자열 xref 모습과 함께 xrefs(cross reference)창이 나타나 해당 변수를 참조하는 모든 주소가 출력된다.

첫번째 항목을 더블 클릭하여 따라가 보면 ```hello```함수를 찾을 수 있다.

![image](https://github.com/user-attachments/assets/c14a5da4-4f0f-48bd-ab46-c722216f13d3)


이번에는 hello 함수를 클릭한 상태로 상호 탐조 단축기 ```x```를 누른다.

![image](https://github.com/user-attachments/assets/d6a41214-021b-4146-bd90-ac5c56794b0b)

그러면 위와 같이 xrefs 창이 뜨고 첫번째 항목을 더블클릭해 따라가보면 아래와 같이 ```main``` 함수가 나타나는것을 알 수 있다. 

![image](https://github.com/user-attachments/assets/d4bbf8d1-e0ad-4803-a8d0-2209584d3604)

main 함수 분석
-----

이제 main 함수를 디컴파일 해보자.

main 함수를 클릭하고 ```F5```키를 누르면 아래처럼 디컴파일된 코드를 확인할 수 있다.

![image](https://github.com/user-attachments/assets/6379a4d1-22c7-4ce5-9566-8a1bd77da33c)


**인자 분석**

```argc```,```argv```,```envp```로 3개의 인자를 받는다고 나온다.

**동작**

1. ```Sleep``` 함수를 호출하여 0.5초 대기한다.

2. ```hello``` 함수를 실행한다.

3. ```cal``` 함수를 실행한다.

4. 0값을 반환한다.

**서브 함수 분석**

1. ```hello``` 함수 : 
   메시지 박스를 띄우는 점을 보아 hello() 함수는 사용자에게 'Hello, swing!'이라는 안내 메시지를 표시하는 역할을 수행하는 함수로 보인다.
   
![image](https://github.com/user-attachments/assets/b012da2e-89e0-483c-962e-14579447a3b1)

2. ```cal``` 함수 : 
   문자열을 생성하여 메시지 박스로 출력하는 점을 보아, cal() 일정한 정수 값을 변수에 저장한 후, 이를 계산하여 메시지 박스를 띄우는 역할을 수행하는 함수로 보인다.

![image](https://github.com/user-attachments/assets/23e78cd0-78df-46a9-984b-990d5d28072d)
