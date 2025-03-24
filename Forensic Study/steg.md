<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] steg Write-up</h1>
</body>
<br>
<br>
</html>

문제 설명
--
**steg.png**

![steg](https://github.com/user-attachments/assets/d0e56dd1-711e-416b-aecc-257e1a5734a8)


문제 풀이
--

HxD로 열어 PNG 파일 시그니처를 확인했다.

![image](https://github.com/user-attachments/assets/cd3ad940-6706-4163-a757-cd057cdab7a1)

![image](https://github.com/user-attachments/assets/5cdc360d-ab90-4038-a3c8-f085472b2b01)

문제는 없어 보인다. 

문제가 steg인 점을 보아 아마래도 스테가노그래피 문제인듯 한데...

스테가노그래피 문제를 어떻게 풀어야 할지 잘 모르겠어서 일단 구글링을 해보았다.

검색을 해보니 스테가노그래피 툴을 사용하면 쉽게 풀 수 있는 것 같다. 
근데 툴의 종류도 너무 많고 뭘 써야 할지 모르겠어서 우선 문서화 하면서 LSB(Least Significant Bit)변조를 통한 정보 은닉 방법이 많이 쓰인다는 것을 배웠어서 여러 툴 중에서도 RGBA 영역을 시각적으로 확인하거나, 특정 비트를 추출하는 등의 작업 가능하다는 온라인 사이트를 사용해보았다.

해당 온라인 사이트 주소는 https://georgeom.net/StegOnline/upload 이다.

사이트에 접속하면 아래와 같이 업로드 하라고 한다.

![image](https://github.com/user-attachments/assets/486915cd-1509-406a-966b-7859be302c46)

ste.png 파일을 업로드해보니 아래 그림처럼 여러 옵션들이 나타난다.

![image](https://github.com/user-attachments/assets/c716e9fc-85bb-41a7-a779-3d33538700ef)

일단 LSB와 관련이 있을 수도 있다는 생각에 LSB Half를 눌러보았더니 업로드한 사진이 아래와 같이 변경되었다.

![image](https://github.com/user-attachments/assets/a5b2e257-fbfd-4104-87df-1f3c8bdaacd6)

사진에서 사람의 형체를 자세히 보면 그전에 보이지 않던 문자가 있는것을 확인 할 수 있었다.

![image](https://github.com/user-attachments/assets/7a87a9b0-7b5d-418a-9e74-fb934a42569e)

L1nux3rr0r이 Flag 인것 같다!!
