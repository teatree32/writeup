<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #crypto #misc Guess this! Write-Up</h1>
</body>
<br>
<br>
</html>

문제 파일을 다운로드 받아보니 알수 없는 문자가 그려져 있는 사진이 있다.
![WHARISTHIS](https://github.com/user-attachments/assets/fe285ab8-42c1-4ccb-a77d-5a50611b4413)

일단 구글에 검색해 봤다.

![image](https://github.com/user-attachments/assets/80c10c72-340a-49f9-b1a2-7fce4126e247)

비슷한 사진들이 많이 나왔는데 그중에서 그나마 비슷한 문자를 찾았다.

![image](https://github.com/user-attachments/assets/a3b39ecb-ccd4-4c11-a700-9c46bc959ec1)

여러문양들을 조합해서 문양을 만드는것 같다.

그럼 첫번째 문자부터 조합해서 숫자를 더해 보았다.
1. 30+900 = 930
2. 3+20+2000 = 2023
3. 8+100+9000 = 9108
4. 7+10+300+7000 = 7317
5. 4+80+100+8000 = 8184
6. 20++400+100+1000 = 1520
7. 8+30+600+2000 = 2638
8. 2+60+800+8000 = 8862
9. 80+300 = 380
10. 20+300+5000 = 5320

조합해 보면 FLAG는 3S{930_2023_9108_7317_8184_1520_2638_380_5320} 일 것 같다.
