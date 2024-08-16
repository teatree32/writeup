<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #crypto Math-RASA Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제 파일을 다운받아 압출을 풀어보니 txt 파일 3개가 들어 있다.

![image](https://github.com/user-attachments/assets/e821fa92-3b22-4041-b87c-fbe14f1ace2c)

hint.txt 파일을 열어보았다.

![image](https://github.com/user-attachments/assets/77ca207d-394f-47d4-bbe4-9787b36a0b7c)

힌트가 말하는 가장 작은 정수는 23이다.

다음으로 문제.txt 파일을 열어보면

![image](https://github.com/user-attachments/assets/60616b69-e02e-4610-8c22-eb075c0b081c)

RSA 문제를 풀라고 한다.

rsa.txt 파일을 열어보면 

![image](https://github.com/user-attachments/assets/2f799f26-a6f0-4de9-92f6-5d2f8f2f9581)

암호화된것 같은 문자열들이 여러개 있다.

RSA 암호를 어떻게 푸는지 잘 모르겠어서 구글링을 해보다 아래 방법을 하나 찾게 되었다.

![image](https://github.com/user-attachments/assets/365351ea-a5c2-420f-aeec-5bc0114ded65)

일단 이 방법대로 시도해보니 자구 디코딩 에러가 떠서 마지막 디코드 코드를 없애고 실행해 보았다.

![image](https://github.com/user-attachments/assets/0e089ad1-8c45-4c49-9f05-d897abea8cad)

![image](https://github.com/user-attachments/assets/55be3aa0-2019-4b2d-a27c-429f018c3672)

16진수와 아스키코드로 디코딩을 시도해 봤지만 FLAG 형식은 나오지 않는다.
아무래도 다른 방법을 찾아봐야겠다....
