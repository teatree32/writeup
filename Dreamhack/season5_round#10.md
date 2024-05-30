<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>5월Dreamhack CTF Season 5 Round #10 pharmacy Write-Up</h1>
</body>
<br>
<br>
</html>

이번 CTF에서도 Web 문제인 Dreamhack CTF Season 5 Round #10 (🌱Div2)에 pharmacy를 풀어보았다.
![image](https://github.com/teatree32/writeup/assets/164837312/48715c36-a1bf-4414-a1e9-0fccfca4fa5b)

우선 문제를 다운받고 압축을 풀어보자.
![image](https://github.com/teatree32/writeup/assets/164837312/4d1bf3d9-7f3c-405d-a03f-a056987dc30a)

일단 파일들을 열어보면 형태는 위와 같다. 각각의 파일들이 무엇인지 생각해보자.
Dockerfile : 이미지를 생성하는 파일이다.
deploy/run.sh : 플레그가 나타나는 쉘 스크립트 파일인것 같다.
deploy/flag : 텍스트 문서인데 정확히 뭔지는 모르겠지만 flag를 얻는데 필요한 파일 같다.
deploy/src/supermarket : 코드를 보면 특정 작업이 끝나면 실행되는 파일같다.
deploy/src/style : 실제 웹 사이트에서 보이는 스타일 시트 파일이다.
deploy/src/index : parmacy 웹 사이트를 나타내고 있는 파일로 처방전 이미지를 업로드할 수 있도록 코드가 만들어져 있는것 같다.
deploy/src/uploads/placeholder : 이미지를 업로드할 때 필요한 파일 같다.

