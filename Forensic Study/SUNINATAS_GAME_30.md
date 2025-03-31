<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] SUNINATAS GAME 30 Write-up</h1>
</body>
<br>
<br>
</html>

문제 설명
--
![image](https://github.com/user-attachments/assets/da087062-4d01-4f7a-a917-376c45f13ae8)


문제 풀이
--

volatility3 사용을 위해 문제 파일 MemoryDump(SuNiNaTas)를 다운로드 받아 volatility3 도구 파일 안에 넣는다.

![image](https://github.com/user-attachments/assets/483a11b7-fe02-4863-96b0-639c9910b1cc)

우선 덤프 파일에 대해 분석 가능한 형태로 프로파일을 추출해보자
```
python vol.py -f "MemoryDump(SuNiNaTaS)" windows.info
```

![image](https://github.com/user-attachments/assets/56184c50-ec23-4f72-8b26-da063925c596)

본격적으로 분석을 시작해보자

PC의 IP 주소를 알기 위해서는 네트워크 행위를 분석하는 플러그인 netscan을 사용하면 된다.
```
python vol.py -f "MemoryDump(SuNiNaTaS)" windows.netscan
```

![image](https://github.com/user-attachments/assets/e2a09179-7484-46bc-b933-48cffc9b8d92)

대부분의 출발지(src) ip가 ```192.168.197.138```인 것을 보아 사용자의 ip는 ```192.168.197.138```인 것을 알 수 있다.

이어서 cmd 창에 입력한 커맨드를 출력하는 플러그인 cmdline을 입력해보았다.
```
python vol.py -f "MemoryDump(SuNiNaTaS)" windows.cmdline
```

![image](https://github.com/user-attachments/assets/38fddd76-fcb2-4908-bf44-ab9f906c7d7c)

메모장으로 **SecreetDocumen7.txt** 파일이 열렸던 기록을 확인 할 수 있었다.

그렇다면 문서의 내용이 무엇인지 파일 복구를 통해서 알아보자

volatility3에서는 메모리 상에 올라갔던 파일의 섹션을 그대로 잘라서 복구가 가능한데 이를 사용하기 위해서는 해당 파일의 정확한 메모리 주소가 필요하다. 

메모리 주소 등 디테일한 파일 정보를 알기 위해서는 filescan 플러그인을 사용하면 된다.

```
python vol.py -f "MemoryDump(SuNiNaTaS)" windows.filescan
```

![image](https://github.com/user-attachments/assets/64ec6977-a7fb-46ab-be95-501c30e03b9c)

**SecreetDocumen7.txt**의 시작 메모리는 ```0x3df2ddd8``` 이었음을 확인할 수 있다.

파일 복구를 위한 플러그인 dumpfiles 를 사용해보자

특정 파일을 명시하기위해 --physaddr 옵션을 사용하여 해당 파일의 물리 메모리 주소를 넣어주었다.
```
python vol.py -f "MemoryDump(SuNiNaTaS)" windows.dumpfiles --physaddr 0x3df2ddd8
```

![image](https://github.com/user-attachments/assets/e2ee6394-ca0c-461e-ba09-7324b15613cd)

실제 경로로 가보면 복구된 파일이 생겨났다.

![image](https://github.com/user-attachments/assets/d3d6369f-8b7f-46f9-9558-4174bcdee55c)

```.dat``` 확장자를 삭제하고 메모장으로 열어주면 key 값이 나오는 것을 알 수 있다.

![image](https://github.com/user-attachments/assets/21193d90-2424-466c-b4ae-6ac703d5dfda)


정리해보면

1) 192.168.192.138 
2) SecreetDocumen7.txt 
3) 4rmy_4irforce_N4vy

그렇다면 인증키는 lowercase(MD5(192.168.197.138SecreetDocumen7.txt4rmy_4irforce_N4vy))

이때 MD5(192.168.197.138SecreetDocumen7.txt4rmy_4irforce_N4vy)는 아래 사이트를 사용해 MD5문자열로 암호화 해주면

https://coding.tools/kr/md5

C152E3FB5A6882563231B00F21A8ED5F 임을 알 수 있다.

이어서 lowercase(C152E3FB5A6882563231B00F21A8ED5F)는 아래 사이트를 통해서 소문자로 변환이 가능하다.

https://www.convertstring.com/ko/StringFunction/ToLowerCase#google_vignette

최종적으로 인증키는 c152e3fb5a6882563231b00f21a8ed5f 이다.

![image](https://github.com/user-attachments/assets/05b9247a-2da0-4b0d-bee9-ff14e3e1fe29)
