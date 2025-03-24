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

![CTF 문제](https://github.com/user-attachments/assets/41fcbc80-3ab0-4aba-bc33-cb73458b9350)

**CTF.jpeg**

<img width="325" alt="CTF" src="https://github.com/user-attachments/assets/75791d14-7ee2-463e-a50d-80b6e961c1cc" />


문제 풀이
--

HxD로 문제 파일 CTF.jpeg를 열어보았다.

![image](https://github.com/user-attachments/assets/b5755bcb-8333-4caf-8a0b-e69cf48cae85)

파일 시그니처를 먼저 확인하는데 어라? 헤더 시그니처가 JPEG 헤더 시그니처가 아니라 PNG 헤더 시그니처다.

푸터 시그니처 PNG의 푸터 시그니처인지 검색해 보았다.

![image](https://github.com/user-attachments/assets/0c89a019-3da6-478e-a4a8-c9f42ed6308e)

PNG의 푸터 시그니처가 존재한다. 그리고 뒤에 문장이 있는데...

Good try! but..it's just a normal picture..so.. there's no hint in this picture..PK 

PK... PK면 ZIP의 헤더 시그니처를 나타내는 것 같다. 아니나 다를까 ```50 4B 03 04```가 있다!! 심지어 3개나!!

![image](https://github.com/user-attachments/assets/a1b2120b-d662-49b4-8e9d-d142c02b8ba4)


그렇다면 푸터 시그니처도 있을 것 같아 검색해보니 2개가 나온다. 엥 헤더는 3개인데 푸터가 2개일 수 있나??

![image](https://github.com/user-attachments/assets/71e1db9a-0eab-40f2-a140-c17977af1d34)

우선 찾은 헤더와 푸터 시그니처에 근처에 있는 문자열들을 쭉 확인해 보았다.

![image](https://github.com/user-attachments/assets/ebcc739d-ced9-4b45-92e4-a7aa6bf98415)

![image](https://github.com/user-attachments/assets/5b4453e4-be89-47bc-a4e0-4ce321e61a2e)

![image](https://github.com/user-attachments/assets/6e200d44-afc5-4eba-a5df-c52207f04fcf)

![image](https://github.com/user-attachments/assets/43c66db4-9ad3-4f12-bd77-226e3e364ba6)

쭉 나열해 보면 ```flag.bmp```, ```Hint1.jpg```, ```Hint2.png``` 이다.

아마 zip파일 추출해서 압축을 풀면 위의 파일들이 나타나는것 같은데... 추출하는 법을 모르겠다...

그래서 바로 구글링을 해보니 헥스 값을 그냥 복사해서 새 파일에 붙여넣기 하면 된다고 한다.

zip 헤더 시그니처부터 끝까지 드레그하여 복사한 후 CTFzip.zip 파일을 새로 만들었다.

![image](https://github.com/user-attachments/assets/ef07d7d6-da1e-4570-bae0-4f525f86799e)

![image](https://github.com/user-attachments/assets/42821d39-30ee-4af4-9db9-0af29bd210c9)

이후 CTFzip.zip 압축을 해제하니 flag.bmp파일이 생겨났다.

![image](https://github.com/user-attachments/assets/d416efa9-2d0c-4cbf-9afa-f4c4194a1b2f)

**flag.bmp**

![image](https://github.com/user-attachments/assets/bdcf995a-ef53-43bd-8beb-4a3fe8961103)


Hxd로 열어서 파일 시그니처를 확인해 보았다.
BMP 파일 시그니처는 ```42 4D```이다.

![image](https://github.com/user-attachments/assets/1fcf62cf-1928-4a09-9102-41e6500cdc75)

크게 문제는 없어보인다. BMP 파일은 푸터 시그니처가 없지만 그래도 한번 확인해 보았다.

![image](https://github.com/user-attachments/assets/6d0b5296-2453-4c5e-ab93-97a50aa01613)

오 문장이 있다

Look at the different Hex values on lines 00000100 to 00005000. The flag begins with FE and consists of a total of 216 bytes.

해석하면 "헥스값 00000100부터 00005000봐라. flag가 FE 로 시작하고 216 바이트크기이다." 이다.

그러면 하라는대로 헥스값을 확인해보자

![image](https://github.com/user-attachments/assets/65c06443-e757-48ef-a622-94bea7b645df)

해당 라인의 헥스값을 선택하여 따로 새 파일로 만들어 보았다.

![image](https://github.com/user-attachments/assets/651574b6-7d5a-4320-aa5d-44e294beffdf)

만든 무제2파일에서 FE를 검색해보았다.

![image](https://github.com/user-attachments/assets/2dc87901-9c0b-4c60-a73d-cef3636ff978)

![image](https://github.com/user-attachments/assets/9f43136c-69ef-46fc-8104-549213b9f99d)

어라 이러면 안되는데?? FE 값이 98개나 나왔다

뭐지 뭔가 많이 잘못 된것 같다.

일단 여기서 더 얻을 수 있는 것은 없는 것 같으니 다시 CTFzip.zip 파일로 돌아가보자

아까 CTF.jpeg 에서 zip 헤더 시그니처가 3개였고 1번째 헤더 시그니처부터 복붙하여 새 파일을 만드니 flag.bmp 파일이 나온것이니 2번째 헤더 시그니처부터 복붙해 새 파일을 만들면 뭐가 하나 더 나오지 않을까? 싶다.

그래서 CTFzip.zip에서 2번째 였던 헤더 시그니처를 찾아 CTFzip2.zip으로 만들었다.

![image](https://github.com/user-attachments/assets/89da0c31-6fb6-4975-a796-294721db3bb2)

만든 CTFzip2.zip 압축을 풀어 확인해보니 Hint1.jpg와 Hint2.png가 생겨났다!!

![image](https://github.com/user-attachments/assets/9debcd95-3807-4ba9-ba9d-d1bd17306ed7)

![image](https://github.com/user-attachments/assets/f07314b4-f325-4929-a555-3c21038fc176)

Hint1과 Hint2를 각각 HxD로 열어보았더니 각각 끝값에서 메세지를 확인할 수 있었다.

![image](https://github.com/user-attachments/assets/3a8d9e61-1e5a-4355-81d0-106816755afd)

![image](https://github.com/user-attachments/assets/4bda886a-2ad7-4f9e-a459-5b05d6125932)

Hint1 : Maybe..you can search for LSB Steganography..and..Always check the end carefully

Hint2 : What you should do is combining LSB

해석하면

힌트1 : LSB 스테가노그래피를 검색하고 항상 끝을 주의 깊게 확인할 수 있습니다.

힌트2 : LSB를 결합하는 것이 좋습니다.

인데...

그러고 보니 flag.bmp 파일에 00000100부터 00005000안에서 특정 부분이 헥스값 FF와 FE로 이루어져 있었고 지금 생각 해보니 배경이 흰색뿐인데 FF가 아닌 FE로 번갈아가며 이루어진것이 LSB 변조인것 같다!!

FF는 1111 1111이고 FE는 1111 1110이니까 최하위 비트만 다른것이다. 이건 LSB가 아닐 수가 없다.

아니 왜 이제서야 보인거지...

그렇다면 FF는 1로 FE는 0으로 보고 시작이 FE고 216바이트라고 했으니 아래 값을 바꾸면 될 것 같다.

![image](https://github.com/user-attachments/assets/98b2918e-b795-435b-85b9-505467edcd18)

```FE FE FF FF FE FE FF FF FE FF FE FF FE FE FF FF FE FF FF FF FF FE FF FF FE FF FF FE FE FF FF FE FE FE FF FF FE FE FE FE FE FE FF FF FE FF FF FE FE FF FF FE FE FF FE FF FE FF FF FE FF FF FF FE FE FF FF FF FE FE FF FF FE FE FF FF FE FE FE FF FE FF FF FE FE FE FF FF FE FF FF FF FE FE FF FF FE FF FE FF FF FF FF FF FE FE FF FF FE FE FE FF FE FF FF FF FE FE FF FF FE FF FE FF FF FF FF FF FE FF FE FF FE FE FF FE FE FF FF FE FE FF FE FF FE FE FF FF FE FF FF FE FE FF FF FE FF FF FE FE FE FF FF FE FF FF FE FE FE FF FF FF FF FE FE FF FE FF FE FF FF FF FF FF FE FF FE FE FE FF FF FE FE FF FE FF FE FF FE FF FE FF FF FE FF FF FF FE FE FF FF FF FF FF FE FF```


파이썬으로 코드를 짜서 헥스 값을 이진으로 바꾸면

![image](https://github.com/user-attachments/assets/1098b375-fd0a-4dd3-a128-a0823853154e)


```001100110101001101111011011001100011000000110110011001010110111001110011001100010110001101110011010111110011000101110011010111110101001001100101001101100110110001101100011110010101111101000110010101010110111001111101```

위와 같은 같이 나온다.

이제 이걸 아스티 코드로 바꾸면 된다. 이건 그냥 변환 사이트에서 했다.

https://www.rapidtables.com/convert/number/ascii-hex-bin-dec-converter.html

![image](https://github.com/user-attachments/assets/5acc55c6-2033-45a9-8de1-6e2309adee75)

**Flag**

3S{f06ens1cs_1s_Re6lly_FUn}
