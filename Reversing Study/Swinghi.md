<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] Swinghi.exe Write-Up</h1>
</body>
<br>
<br>
</html>

swinghi.exe 파일을 IDA로 열어보았다.

-문자열 검색
```shift+F12```키를 눌러 바이너리에 포함된 문자열이 결거된 String 창을 확인한다.

![image](https://github.com/user-attachments/assets/884ef280-59fe-421c-b671-1db788e1dc2d)

이때 "Hello, swing!"이라는 문자열은 컴파일 과정에서 삽인된 문자열이 아니라 프로그래머가 추가한 문자열 같다.

해당 문장열을 더블 클릭하여 따라가보자.

![image](https://github.com/user-attachments/assets/a03780f8-4611-4b41-89dd-6989d4b70a6a)

-상호 참조
"aHelloswing"을 클릭한 상태로 상호 참조 단축키 ```x```를 누른다.

![image](https://github.com/user-attachments/assets/89edd0c2-1fb4-4732-a49c-12fc96540630)

그러면 위와 같이 문자열 xref 모습과 함께 xrefs(cross reference)창이 나타나 해당 변수를 참조하는 모든 주소가 출력된다.

첫번째 항목을 더블 클릭하여 따라가 보면 ```hello```함수를 찾을 수 있다.

![image](https://github.com/user-attachments/assets/c14a5da4-4f0f-48bd-ab46-c722216f13d3)


이번에는 hello 함수를 클릭한 상태로 상호 탐조 단축기 ```x```를 누른다.

![image](https://github.com/user-attachments/assets/d6a41214-021b-4146-bd90-ac5c56794b0b)

그러면 위와 같이 xrefs 창이 뜨고 첫번째 항목을 더블클릭해 따라가보면 아래와 같이 ```main``` 함수가 나타나는것을 알 수 있다. 

![image](https://github.com/user-attachments/assets/d4bbf8d1-e0ad-4803-a8d0-2209584d3604)
















