<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>webhacking old-31 Write-Up</h1>
</body>
<br>
<br>
</html>

문제파일을 클릭을하면 아래와 같은 코드와 경고 문구가 나온다.
![image](https://github.com/user-attachments/assets/4e6145a4-7d69-4a74-b685-ea7f806ffb20)

먼저 코드를 읽어보자.
```python
$port = rand( 10000, 10100 );
 ```
10000 ~ 10100 사이의 임의 포트 번호를 생성한다는 의미같다.

```python
$socket = fsockopen($_GET['server'],$port,$errno,$errstr,3) or die("error : {$errstr}");
```
fsockopen 함수를 사용해, 주어진 IP 주소와 포트로 소켓을 열고 $_GET['server'] 를 통해 사용자 입력 값을 받아오는데, 이것이 서버의 주소로 사용되는것 같다.
 
다음으로 Warning 메시지를 보아, 소켓 연결에 실패한 것을 알 수 있고, 그 이유가 연결이 거부되어 실패했다는 것을 알 수 있다. 즉, 해당 포트가 서버에서 열려있지 않거나, 방화벽등의 이유로 연결이 거부되었을 가능성을 유추해 볼 수 있다. 즉, 이번 문제는 포트포워딩을 해야 하는 문제인 것 같다. 


일단 우리집 와이파이 설정페이지에 접근해서 포트포워딩 설정을 해주었다.
![스크린샷 2024-09-09 221229](https://github.com/user-attachments/assets/5c69e68f-9448-4ada-b78d-0675695a4155)

그리고 netcat 프로그램을 설치해 다음과 같이 명령어를 입력해주고 문제 사이트를 새로고침해주면 FLAG를 얻을 수 있다!!
![image](https://github.com/user-attachments/assets/4b6780b4-dc3b-4c1b-8e4d-3142f47a0bcc)


