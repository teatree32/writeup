<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Webhacking old-43 Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 들어가니 웹쉘을 업로드하고 flag를 출력하라고 한다.

![image](https://github.com/user-attachments/assets/3a2c7e87-66a9-4ba7-b6b0-87d31e643e87)

우선 시키는 대로 shell.php 파일을 만들었다.
```pyton
<?php
    readfile("/flag");
?>
```

만든 shell.php 파일을 업로드 해보았다.

![image](https://github.com/user-attachments/assets/ff8a2a38-ca86-41df-ac26-576eafa4996a)

![image](https://github.com/user-attachments/assets/9722ab86-bf9d-4ca4-9aa8-93d9f8b761da)

잘못된 타입이라고 나온다. 

일단 어떤 파일들을 업로드할 수 있는지 알아보기 위해 개발자 도구를 통해 Request Headers의 Accept를 확인했다.

![image](https://github.com/user-attachments/assets/007735fd-d616-437b-b6ca-445e42959f15)

+Accept Headers란?
데이터 타입(MIME)을 다루는 헤더이다. 
Accept 헤더는 클라이언트가 서버에게 어떤 특정한 데이터 타입을 보낼때 클라이언트가 보낸 특정 데이터 타입으로만 응답을 해야한다.

Accept Headers를 살펴보니 image/apng가 가능하다.

Burp Suite를 활용해서 데이터 타입을 바꾸면 업로드가 가능할 것 같다.

![image](https://github.com/user-attachments/assets/90c195bd-2fcc-4b76-8a03-37849132f00c)

![image](https://github.com/user-attachments/assets/857f2da5-7e4e-486b-bb5a-4abc6924673b)

정상적으로 업로드가 되었다. 

업로드된 파일로 들어가보자!

![image](https://github.com/user-attachments/assets/c291622b-0ee3-42fa-8312-7a611886aea2)

flag가 나왔다!!
