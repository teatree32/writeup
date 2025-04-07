<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] CTF Write-up</h1>
</body>
<br>
<br>
</html>

문제 설명
--
![Network Recoverly! 문제](https://github.com/user-attachments/assets/ad4ccecf-ec55-4c3d-9572-b3ab4051c1a7)

문제 풀이
--
우선 문제 이름부터 Network 라고하니 WireShark로 열어보았다.

![image](https://github.com/user-attachments/assets/aaf7c3fb-9691-4c3b-b234-c07589ae3ab7)

http에서 get 요청하는게 눈에 띈다. 자세히 살펴보자.

![image](https://github.com/user-attachments/assets/e065324f-7f8f-4939-8ed9-6cc28320f0de)

index.html, treasure1.png, treasure2.text, treasure3.text get 요청하고 있다. 

우선 treasure1.png 파일을 HxD로 열어보았다.

![image](https://github.com/user-attachments/assets/372ef52f-bfcb-4d17-9e82-298e3f8670b0)

png 헤더 시그니처는 있는데 푸터 시그니처가 없다.

일단 놔두고 treasure2.text를 열어보았다.

![image](https://github.com/user-attachments/assets/33d372ed-2ee4-432f-b24f-132467c26290)

다 0으로 이루어져 있다.

treasure3.text도 열어보았다.

![image](https://github.com/user-attachments/assets/fea2b9f1-8ee4-4df6-867f-b12579aefb27)

앞부분은 다 0으로 이루어져있고 끝에 png 푸터 시그니처가 있다. 

treasure 1,2,3 파일 핵스 값을 쭉 연결하면 될 것 같다.

treasure1.png에 헥스값을 이어서 붙이고 저장해보니 사진이 나타났다.

![image](https://github.com/user-attachments/assets/d6bc6c51-698d-44e2-9d8c-a13651e5334a)

Key값은 ``` NET\oFK1sFun```

Key Format = lowercase(md5("key"))

먼저 아래 사이트에서 md5 암호화하면 ```02EABA780C2833E2D27AFE6A58109E93```

https://coding.tools/kr/md5

이어서 lowercase(소문자화)하면 ```02eaba780c2833e2d27afe6a58109e93```

Key Format = 02eaba780c2833e2d27afe6a58109e93 이다.
