<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Webhacking old-48 Write-Up</h1>
</body>
<br>
<br>
</html>

문제서버로 들어가 보자

![image](https://github.com/user-attachments/assets/688e36ac-fd71-4ce5-8640-e0c8422d258d)

뭔지모르겠지만 언니들 이름과 시간같은게 떠 있다.(아마 언니들의 사투의 흔적 같다... 내 미래인가...)

일단 php 파일을 업로드 해봤다.

```python
<?php
    system("cat /flag");
?>
```

![image](https://github.com/user-attachments/assets/247900de-70c3-4f4c-aa7f-f00f55bfa794)

![image](https://github.com/user-attachments/assets/ee180b6c-c470-4df7-a6cd-efc78e7d7f32)

내가 만든 teatree가 생겼다. upload file을 눌러 보니 내가 업로드한 php파일의 코드가 출력되고 있다.

![image](https://github.com/user-attachments/assets/4730cb45-a8bd-4a57-86eb-3401e284ea15)

Delete를 눌렀더니 파일이 지워졌다.

![image](https://github.com/user-attachments/assets/55c182c2-db73-4749-9c15-5a2df7ab9cec)

뭐지 뭔지하나도 모르겠다...

열심히 구글링해보니 업로드한 파일이 삭제될 때, 'rm /upload/파일명' 명령어가 실행되고 file deleted 창이 뜬다고 한다.

그러므로 rm 명령어 뒤에 우리가 원하는 명령어를 입력하면 입력한 명령어가 실행된다. 

즉, 명령어를 더 실행시키기 위해서는 파일명에 세미콜론(;)을 붙인 다음 명령어를 입력하면 된다.

Burp Suite를 사용해서 파일명을 바꿔주었다.

![image](https://github.com/user-attachments/assets/4d029056-8903-493a-9067-33eca5f65cfe)

![image](https://github.com/user-attachments/assets/aa8c4bc6-06f4-47c3-b785-0429b33bab33)

파일이 업로드 되었다. Delete를 눌러보았다.

![image](https://github.com/user-attachments/assets/7a429861-dc88-419e-831a-b504d80a45f6)

플래그를 나왔다!!
