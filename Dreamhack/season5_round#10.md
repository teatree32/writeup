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

그럼 파일을 하나하나 분석해 보자. 먼저 Dockerfile 파일 코드를 해석해보겠다.

![image](https://github.com/teatree32/writeup/assets/164837312/01020f3f-7471-410c-b10d-66e348c39b47)

4월달 CTF에서 공부해본 내용과 겹치는 내용이 있지만 chmod 777 은 처음 보는 코드라서 검색해 보았다.
파일 사용 권한은 숫자 또는 기호 형식으로 나타낼 수 있는데 r(읽기) = 4, w(쓰기) = 2, x(숫자) = 1을 의미한다. 이때 7은 4+2+1로 읽기 쓰기 및 실행권한을 가지는 것을 의미한다.

풀이 : php:7.3-apache 이미지에서 시작해 ./deploy/run.sh/usr/svin/ 경로에 있는 파일을 가져오고 새로운 레이어에서 /usr/sbin/run.sh 실행권한을 추가한다. 그리고 ./deploy/src/var/www/html 파일을 가져오고 새로운 레이어에서 /var/www/html/uploads 읽기 쓰기 및 실행권한을 추가한다. 이어서 80포트로 이미지를 열고 /usr/sbin/run.sh을 실행한다.

이어서 run.sh을 살펴보자.

![image](https://github.com/teatree32/writeup/assets/164837312/cf74bc1d-deb8-44ec-b1a5-2d6412370e92).

4월달 CTF에서 본 run.sh와 같은 것을 알 수 있는데 아직도 export 명령어를 통해 지역변수를 전역변수로 만드는 이유는 잘 모르겠다.

다음으로 flag 파일을 살펴보자.

![image](https://github.com/teatree32/writeup/assets/164837312/75712750-cb69-4b3a-bb1e-edc07f52b45c)

그냥 텍스트 파일로 placeholder 라고 적혀있다. 무슨 뜻이 있을까 싶어 검색애 보았다.
HTML의 placeholder 속성은 사용자가 어떤 것을 입력해야 할지 안내해 주고 입력하면 사라지는 힌트를 표시할 때 사용하는 속성으로 밑에와 같은 형태로 사용된다.

![image](https://github.com/teatree32/writeup/assets/164837312/9dcf8fa8-e109-4fa2-829f-6ae0014156a1)

하지만 flag 파일에는 placeholder 라고만 쓰여있어서 정확히 위와 같은 뜻을 가지는지는 잘 모르겠다.

이어서 supermarket 파일을 살펴보자.

![image](https://github.com/teatree32/writeup/assets/164837312/086203cc-d93f-4aeb-a293-e8e870529ad3)

코드를 살펴보면 goodbye 함수를 만들고 Supermarket 클래스를 만든것을 확인할 수 있다. 무슨 용도인지는 정확히 모르겠지만 index 파일에서 용하려고 만들어진것 같다고 추측해본다.

다음으로 style파일을 살펴보자.

![image](https://github.com/teatree32/writeup/assets/164837312/1c58b6af-7f4c-444d-9778-b19a86f85d2d)

index파일의 스타일을 나타나내느 css 파일임을 알 수 있다.

이어서 index 파일을 살펴보자.
![image](https://github.com/teatree32/writeup/assets/164837312/20d0292c-ae48-4fc1-9116-44b6baeb13bb)
![image](https://github.com/teatree32/writeup/assets/164837312/96cf1cc8-092f-43cf-afb8-e021c98bc69a)

코드를 살펴보면 supermarke.php와 style.css가 연결된 것을 확인할 수 있다.



