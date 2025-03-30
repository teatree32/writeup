<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Reversing Study] Easy_ELF Write-Up</h1>
</body>
<br>
<br>
</html>

우선 문제파일을 실행해 보았다.

![image](https://github.com/user-attachments/assets/0354b539-de50-4311-b4d6-e550f47ceff0)

권한이 없다고 나와서 실행권한을 부여해주고 열어 보았다.

아무 문자열을 입력해 주었더니 Wrong이라고 뜬다. 

바로 IDA로 열어보자

![image](https://github.com/user-attachments/assets/77bacaa0-5134-4353-944a-3dbfc035e2ea)

main 함수를 살펴보자

![image](https://github.com/user-attachments/assets/4e033124-1d38-4344-9e40-4c1b5d41ad30)



```sub_8048434()```는 문자열을 입력받는 것 같다.

![image](https://github.com/user-attachments/assets/5eff038e-659d-48df-bc45-e77e4ea6d310)

```sub_8048451()```는 뭔가 복잡한 조건식들이 있다.

![image](https://github.com/user-attachments/assets/cd631e17-42bc-40af-b142-634351524857)

```sub_80484F7()```은 correct 문자열이 출력되는 것 같다.

![image](https://github.com/user-attachments/assets/d2f11adf-4fcb-416b-a22a-3ea710120f67)

전체적으로 코드를 봤을 때 ```sub_8048451()``` 조건에 맞는 문자열을 입력하여 리턴값이 1이 되면 correct를 얻을 수 있는 것 같다.

```sub_8048451()```의 전체적인 흐름을 보면 다음과 같다.

![image](https://github.com/user-attachments/assets/4af6af37-676f-43eb-aec0-1a7c38b661cb)


그러면 ```sub_8048451()``` 조건 하나하나씩 분석해보자

![image](https://github.com/user-attachments/assets/35f37a4e-bd3b-49e8-92eb-54bee64d9a45)

먼저 ```byte_80A4021```의 값과 ```31h```를 비교한다. ```31h```는 아스키로 ```1```이다.

그렇다면 ```byte_80A4021``` = ```1```인것 같다.

이어서 ```byte_804A020``` 값과 ```34h``` xor 연산하고 

```byte_804A022``` 값과 ```32h``` xor 연산하고

```byte_804A023``` 값과 ```0FFFFFF88h``` xor 연산하고

```byte_804A024``` 값과 ```58h```과 비교한다. ```58h```은 아스키로 ```X```다.

그렇다면 ```byte_804A024``` = ```X``` 이다.

![image](https://github.com/user-attachments/assets/d34a7f0d-a5c0-411a-8d99-a1f3e537ae82)

계속해서 ```byte_804A022```와 ```7Ch```와 비교하고

```byte_804A020```와 ```78h```와 비교하고

```byte_804A023```와 ```0DDh```와 비교한다.

byte 순서대로 정리해보면

```byte_80A4020``` xor ```34h``` = ```78h```

```byte_80A4021``` = ```1```

```byte_80A4022``` xor ```32h``` = ```7Ch```

```byte_80A4023``` xor ```0FFFFFF88h``` = ```0DDh```

```byte_80A4024``` = ```X```

이렇게 된다.

```byte_80A4020```, ```byte_80A4022```, ```byte_80A4023```을 구하기 위해 역으로 계산하려면  

```byte_80A4020``` = ```78h``` xor ```34h```

```byte_80A4022``` = ```7Ch``` xor ```32h```

```byte_80A4023``` = ```0DDh``` xor ```0FFFFFF88h```


위와 같이 하면 된다. 

파이썬으로 구현해 보았다.
```
input = ""

input_0= 0x78 ^ 0x34
input+=chr(input_0)

input_1=1
input+=str(input_1)

input_2=0x7C ^ 0x32
input+=chr(input_2)

input_3= 0xDD ^ 0x88
input+=chr(input_3)

input_4='X'
input+=str(input_4)

print(input)
```

![image](https://github.com/user-attachments/assets/7ac50b6b-0540-4515-9496-21b9f07788be)

출력값을 입력해보자

![image](https://github.com/user-attachments/assets/3cfe0980-ddcb-46cc-9456-7ec4a02d80e3)

정답이다!!
