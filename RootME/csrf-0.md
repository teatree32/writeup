<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Root ME Web Hacking csrf-0 Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 들어가자.
![image](https://github.com/user-attachments/assets/eea7cd75-a295-4d4b-b4d9-cd98d464479b)

로그인 창이 뜬다.

위에 ```Register```에 들어가도 똑같이 Username과 Password 입력 칸이 나온다. 여기서 회원가입을 하고 로그인 하면 되는것 같다.

일단 1234로 회원가입하고 로그인 해봤다.
![image](https://github.com/user-attachments/assets/8fd27b3f-6946-4008-84b8-27095099c42c)
![image](https://github.com/user-attachments/assets/3b634dab-5fa4-4cca-8104-764516cceca9)
![image](https://github.com/user-attachments/assets/e6138653-f20a-402a-8254-a7a720763b72)

로그인하니 ```Profile```페이지가 생겼다.
![image](https://github.com/user-attachments/assets/e515df6b-ab0e-49db-a8f2-168f870bcdd3)

들어가보니 ```Contact```, ```Profile```, ```Private```, ```Logout```이 새로 생겼는데 ```Private```는 관리자여야만 접근 할수 있다고 한다.
![image](https://github.com/user-attachments/assets/2150a579-6265-4da5-a597-882946684a67)

관리자 권한을 얻으려면 ```Profile```에서 ```Status```를 체크하면 될것 같은데 체크가 박스가 되지 않는다.
![image](https://github.com/user-attachments/assets/507c5f73-489f-4411-bbff-5453e528203b)

csrf 문제이니 ```Contact```에서 명령어를 입력해서 ```Status``` 체크박스가 체크되게 하면 될것 같다.

구글링을 통해서 form 태크에 접근할 수 있는 명령어들을 찾아 보았다.
javascript:document.getElementById("id 값").submit 명령어를 사용해서 form태그에 강제 구독하도록 만들면 된다고 한다.

우선 ```Contact``` 페이지에서 개발자 도구를 열어서 Form 부분 코드를 싹 긁어왔다.
![image](https://github.com/user-attachments/assets/c3e8f1b3-d1f2-41c2-b046-84803755110e)

불필요한 태그들을 지우고 뒤에 위에쓴 명령어를 추가해보았다.
