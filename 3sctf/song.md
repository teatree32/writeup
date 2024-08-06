<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #crypto what's this song Write-Up</h1>
</body>
<br>
<br>
</html>

파일을 열어보자

![image](https://github.com/user-attachments/assets/4d092513-26a2-4686-bf63-ec7bed1eabb2)

먼저 hint.txt 파일을 열어보니 빈도분석과 아스키코드에 대한 설명이 있다.
다음으로 malware.py를 살펴보니 이 파일이 암호화하는 파일인것 같다. 
그리고 이 파일의 결과가 output.txt 인것 같다.

output.txt 파일을 열어보니 의미를 알 수 없는 문자들이 나열되어 있다.
![image](https://github.com/user-attachments/assets/968a5c05-fbea-4896-a2b5-e1d243d07f3c)
내 예상대로 암호화된 파일이 맞는것 같다.

hint.txt 파일에 써 있던 빈도분석을 통해 암호문을 풀 수 있을것같아 빈도분석에 대해 알아보았더니 알파벳의 모든 문자가 같은 빈도로 쓰이지 않는다는 점을 이용하여 해독하는 방법인것 같다.
![image](https://github.com/user-attachments/assets/789b00c1-3742-48ef-8641-c574eb29c46e)
보통 알파벳 E는 사용량의 12% 넘게 차지하는 반면 J, Q, X, Z는 1%도 차지하지 않는다. 그러므로 암호문에서 가장 많이 쓰이는 문자를 ‘E’로 다시 치환하는 것으로 해독을 시작할 수 있다.또한, 단어 ‘THE’나 ‘THIS’가 가장 많이 쓰인다는 것과 한 글자 단어는 ‘A’나 ‘I’가 많다는 것도 이용할 수 있다. 이러한 여러가지 힌트를 따라서 풀다 보면 완전히 해독할 수 있다고 한다.
그래서 우선 가장 많이 쓰인 문자를 찾기 위해 파이썬으로 빈도 상위 10개 문자를 출력해 보았다.
![image](https://github.com/user-attachments/assets/492741b1-8b01-4805-88fd-bac89e9c63b0)
![image](https://github.com/user-attachments/assets/cd6b66bc-36ce-40d3-a900-8a6ebfbafcaa)

사용된 빈도별로 문자를 대응해 보았다.
'8' -> 'e'
'd' -> 't'
'1' -> 'a'
'c' -> 'o'
'4' -> 'i'
'5' -> 'n'
'0' -> 's'
'e' -> 'h'
'3' -> 'r'
'7' -> 'd'
'6' -> 'l'
'b' -> 'u'
'f' -> 'c'
'a' -> 'm'
'9' -> 'w'
'2' -> 'y'

바꿔서 다시 암호문을 써보면 
![image](https://github.com/user-attachments/assets/a04cce36-b8fc-4b4b-b4b2-6cf37a1372a0)
암호가 완벽하게 해독되지 않는다. 단순 치환 암호가 아닌것 같다.

다른 방법을 찾아봐야겠다.
