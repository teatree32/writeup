<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Block CTF 2024 #Cryptography Sizer_Cipher Write-Up</h1>
</body>
<br>
<br>
</html>

![image](https://github.com/user-attachments/assets/4ccb3931-84ba-453a-9ce5-e537b0d4aca8)

문제를 살펴보면 암호를 찾아내면 되는 것 같다.

우선 문제 서버로 접속해 보았다.

문제에 있던 암호문을 넣어 보았다.

![image](https://github.com/user-attachments/assets/ba422c47-c8db-4481-ac6c-9b8429b7df0e)

더 길어지기만 했다...

Hex값인 것 같아서 돌려보니 결과가 이상하다.

![image](https://github.com/user-attachments/assets/876f9fe3-d2bd-466f-bc44-c79cdc5969c1)

Base64로도 돌려봤지만 결과가 이상하다.

![image](https://github.com/user-attachments/assets/6f79decf-f754-4d81-8a8c-11fb246c4b78)

뭔지 아무것도 모르겠어서 일단 문제 서버에 이것저것을 입력해 보았다. 

그런데 규칙이 보인다!!

![image](https://github.com/user-attachments/assets/42e70a25-a4f7-423f-9cf7-f1c015ed0f78)

1, 2, 3을 입력하면 차례로 결과값이 35, 36, 37이 된다.

그래서 모든 문자들을 입력해보았다.

![image](https://github.com/user-attachments/assets/73d0cd76-5c12-4444-8b23-7d02eb928ba8)

00부터 3f까지 숫자들이 나온다.

암호문을 2개씩 나눠서 매칭하면 될는 건가 싶었지만....

문자를 연속해서 입력하면 값이 바껴진다.

![image](https://github.com/user-attachments/assets/900f74da-4c76-400c-b080-1aaea902896b)

와 도저히 뭔지 모르겠다. 

