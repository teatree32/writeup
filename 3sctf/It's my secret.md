<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #android #REV It's my secret Write-Up</h1>
</body>
<br>
<br>
</html>

NOX emulator를 사용해서 다운로드 받은 문제 파일을 열어보았다.

![image](https://github.com/user-attachments/assets/b82cfbd7-dd84-4815-9e70-40a00fd3b96d)

위의 그림처럼 버튼을 누르면 문장들이 하나씩 나오는 형식이었다. 

APK 파일 분석을 위해 jadx-gui tool을 사용해 문제 파일을 열어보았다.

파일이 엄청 많은데 안드로이드 문제가 처음이다보니 어디서부터 접근해야 할지 막막했다. 

그래서 그냥 파일을 하나하나 열어보다보니 secretactivity 파일에서 FLAG를 찾을 수 있었다.

![image](https://github.com/user-attachments/assets/08729eb3-2897-46ff-b2aa-29c850e69d46)

이렇게 하는게 맞는지는 잘 모르겠지만 일단 FLAG 획득 성공!!
