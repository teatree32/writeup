<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #Forensic CUTE_TIGER Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 README.pdf에 있는 주소로 들어가 image.jpg 파일을 다운받았다.

헥스를 이용해서 이미지 파일을 열어보았다.

jpg 파일의 헤더부분과 푸터부분을 확인해보니 jpg 파일의 헤더/푸터 시그니처와 일치했다.

![image](https://github.com/user-attachments/assets/6cb164ca-d987-40d9-ae8f-f2e906cd3cc4)
![image](https://github.com/user-attachments/assets/9cf2e93a-1a27-42a2-be9f-db4b7e635a22)

푸터시그니처가 두개일 수 있어 검색해보니 하나가 맞았다.

![image](https://github.com/user-attachments/assets/f0ecc37a-8ee3-4ec8-91fa-da273ac965e2)


이어서 FLAG 형식인 3S를 문자열에서 찾아보았다. 두개가 나오지만 둘다 FLAG는 아닌것 같다.

![image](https://github.com/user-attachments/assets/365afdb5-6bd8-48df-942d-f9507bc71f17)

![image](https://github.com/user-attachments/assets/a7c17304-895b-4507-a6f9-f1edb87db38f)

이 외에는 어떻게 문제에 접근해야 할지 잘 모르겟다......

