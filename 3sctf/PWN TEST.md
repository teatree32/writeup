<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #PWN PWN TEST Write-Up</h1>
</body>
<br>
<br>
</html>

문제 파일을 열어보아니 파이썬 파일이 하나 들어있는것을 확인할 수 있었다.

![image](https://github.com/user-attachments/assets/1f11b450-b938-4a14-9df9-7814dbbd7628)

파일을 살펴보니 실행시키면 문제가 나올것 같아 파일을 실행시켜보았다.

![image](https://github.com/user-attachments/assets/23706b7d-9f47-4823-82bf-2b8719996cff)

파일을 실행시켜보니 정체를 알 수 없는 문자열이 나타났다.

무슨 문자열인지 잘 모르겠어서 검색해보니 ELF 실행파일일 수 있다고 생각해 확인해 보았다.

먼저 test1.py라는 파이썬 파일을 만들어 안에 문자열을 파일로 저장하는 저장하는 코드를 작성했다.

![image](https://github.com/user-attachments/assets/2149419c-16fe-4fbb-bbd0-65280e2fa7e4)

![image](https://github.com/user-attachments/assets/34dcdc7b-9252-43aa-b0bb-2b8d4908ac94)

그리고 이어서 파일을 실행시켜보니 

![image](https://github.com/user-attachments/assets/85396011-31d7-4903-83c8-e84bd6edc88d)

그냥 아스키 텍스트 파일이라고 뜬다.

아.... 내가 예상한 결과는 ELF 파일이었는데....

ELF 파일이 아니거나 이렇게 파일을 저장하는게 아닌가 보다.

혹시나 하는 마음에 flag 형식이 있는지 검색해보았다.
![image](https://github.com/user-attachments/assets/79e83249-b3ad-40a8-aa8e-4c30d0981eea)
![image](https://github.com/user-attachments/assets/77563ef3-65eb-4b27-8e27-bc5de10c6df3)

역시나 나오지 않는다.

이 방법들 외에는 어떻게 해야할지 잘 모르겠다....
