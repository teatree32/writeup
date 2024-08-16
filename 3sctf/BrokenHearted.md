<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #Forensic BrokenHearted Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제 파일을 다운받아 압축을 풀어보니 png 파일 하나와 Hint.txt 파일이 있었다.

먼저 Hint.txt 파일을 열어보니

![image](https://github.com/user-attachments/assets/61e9541b-8889-43fa-8dfa-5a9b6da802f3)

라고 써져있어 png 파일을 헥스로 열어보았다.

png의 헤더/푸터 시그니처를 확인해보니 png 파일 형식에 맞다

![image](https://github.com/user-attachments/assets/255ad3be-7829-44ff-8dea-1646800fa5c1)

flag나 flag 형식을 입력해보아도 나오는 것이 없다.

![image](https://github.com/user-attachments/assets/6688ae85-0d0e-4f5c-ab80-4f63e8ed217f)

![image](https://github.com/user-attachments/assets/52975922-3108-4c26-bfc7-f7e6128394ba)

이 방법들 외에는 어떻게 해야할지 잘 모르겠다....
