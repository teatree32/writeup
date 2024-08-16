<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #REV #misc Can solve without thinking Write-Up</h1>
</body>
<br>
<br>
</html>

문제 파일을 다운로드 받아 압축을 풀어보니 뭔지 모를 파일이 하나 들어있다.

![image](https://github.com/user-attachments/assets/6e16c643-69ed-40aa-b3c5-d6304cc117a3)

일단 무슨 파일인지 알아보기 위해서 리눅스를 이용해서 다음과 같이 명령어를 입력해보니 ELF 파일이라고 뜬다.

![image](https://github.com/user-attachments/assets/e23688b9-cb07-4648-9da3-a66b8938ac3d)

이런 형식의 문제를 어떻게 푸는지 모르겠어서 검색하다 보니 ELF 파일 문제를 풀때 strings 명령어를 이용하는것 같아 한번 시도해 보았다.

![image](https://github.com/user-attachments/assets/5540562e-f47c-430e-b505-d3ab5d633ec9)

특별히 FLAG로 추정되는 문자열은 찾지 못했다.

다른 방법으로 권한설정을 해서 파일을 실행시켜보라고 해 따라서 시도해봤다.

![image](https://github.com/user-attachments/assets/cdbb32f6-6d1e-4605-b7a1-4b92343efd06)

아... 오류가 계속 뜨고 열리지 않는다...

아무래도 이 방법들이 아닌것 같다....
