<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Lord of SQL Injection orc Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제를 살펴보자

![image](https://github.com/user-attachments/assets/ec2c1364-0b3c-4ae0-b130-08398030d0ca)

코드를 읽어보면 먼저 눈에 띄는건 

```python
$_GET[pw] = addslashes($_GET[pw]);
```

```addslashes()```에 의해 싱글쿼터가 차단되어 있다. 

+```addslashes()```란?

```addslashes()```는 싱글 쿼터('), 더블 쿼터("), 백슬래시(\), 널 문자(NULL)에 역슬래시(\)를 추가해 이스케이프한다.

예를 들어, pw가 문자열로 입력된다면 ```addslashes()```가 적용될 때를 생각해보자

```python
$password = addslashes($pw);
```

이 경우 pw 값이 a'b와 같은 문자열일 경우 ```addslashes()``를 거치면 a\'b로 저장된다. 

이처럼 입력된 값에 자동으로 역슬래시가 추가되므로, 쿼리에서 특수 문자를 활용해 SQL 인젝션을 시도하는 것이 어려워진다.

즉, ```addslashes()``` 함수는 문자열에서 SQL 쿼리나 다른 데이터베이스 명령어에 사용될 수 있는 특수 문자를 이스케이프 처리하여 SQL 인젝션을 방지한다.



다시 돌아와서 계속 코드를 살펴보자

```python
if(($result['pw']) && ($result['pw'] == $_GET['pw'])) solve("orc");
```

쿼리 실행 결과로 출력된 result의 pw와 GET 방식으로 전달받은 pw가 같아야만 문제를 풀 수 있다.

즉, 패스워드를 직접 알아내야하는 문제인 것 같다.

```python
$query = "select id from prob_orc where id='admin' and pw='{$_GET[pw]}'"; 
echo "<hr>query : <strong>{$query}</strong><hr><br>"; 
$result = @mysqli_fetch_array(mysqli_query($db,$query)); 
if($result['id']) echo "<h2>Hello admin</h2>";
```

위의 쿼리는 싱글 쿼터를 차단하지 않는다. 

또한 해당 쿼리를 참으로 만들면 "Hello admin"이라는 메시지가 출력 되고

![image](https://github.com/user-attachments/assets/e02b192c-cce5-4c47-b078-bf221cfe7b26)

거짓으로 만들면 메시지가 출력 되지 않는다. 

![image](https://github.com/user-attachments/assets/6e7c069f-1364-4ebb-8a44-f0d63618e17b)


Blind SQL Injection은 참/거짓을 통해서 화면이 다르게 출력되는 것을 이용해서 보이지 않는 정보를 출력되도록 하는 공격기법이다.

Blinde SQL Injection을 사용해 위 부분을 통해 패스워드를 알아낼 수 있을 것 같다.

일단 admin 패스워드만 알아내야 하기 때문에 or을 사용해서 앞선 문장은 무시하도록 했다.

만약에 or을 사용하지 않는다면 admin 말고 다른 id의 pw가 참일 때도 메시지가 출력될 수도 있다.

이어서 패스워드의 길이를 알아보기 위해 ```length()```를 사용해 커드를 작성해보자

우선 10보다 작다고 입력해보았다.

![image](https://github.com/user-attachments/assets/224ebdc0-e9aa-4078-a674-b11e3e47570b)

'Hello admin'이 출력된다. 즉, 패스워드길이가 10보다 작은 것이 참이라는 뜻이다.

이어서 패스워드 길이가 9라고 입력해보았다.

![image](https://github.com/user-attachments/assets/ed148c4a-dfb8-490d-b6c8-75618f72d19a)

'Hello admin'이 출력되지 않는다. 즉, 패스워드 길이가 9가 아니라는 뜻이다.

이번에는 패스워드 길이가 8이라고 입력해 보았다.

![image](https://github.com/user-attachments/assets/4fa7b8d4-7195-41ae-bb7a-0be4c212957a)

'Hello admin'이 출력된다!! 패스워드 길이는 8이다.

계속해서 ```substr(문자열, 시작위치, 길)```를 이용해서 pw를 한글자씩 가져와 각 자리의 값을 찾아보자

![image](https://github.com/user-attachments/assets/ae54aaa8-8158-4348-844c-3bdcce36b5e7)

![image](https://github.com/user-attachments/assets/e99b8498-cd2f-4aa6-afac-f0f7765fc71f)

![image](https://github.com/user-attachments/assets/94e48c9f-a078-465b-92a2-b64bada175d3)


3번째 자리까지는 0~9까지의 숫자를 넣어 'Hello admin'이 출력되는 것을 확인 하였다.

각 자리는 순서대로 '0', '9' '5' 이다.

그러나 4번째 자리는 0~9를 넣어도 'Hello admin'이 출력되지 않는다. 아무래도 문자인 것 같다.

그래서 ```ascii()```를 사용하여 값을 찾아 보았다.

![image](https://github.com/user-attachments/assets/c4d4b9ba-2c0e-4160-8467-7442cb65f8af)

먼저 아스키값이 115보다 크다고 입력해 보았다.

![image](https://github.com/user-attachments/assets/34fa8f9b-87a5-4a0f-bf16-4fbe4036a6f5)

'Hello admin'이 출력되지 않는다. 

이번에는 아스키값이 100보다 크다고 입력해 보았다.

![image](https://github.com/user-attachments/assets/8871beb8-7661-446c-82d3-4919490acc0f)

'Hello admin'이 출력되지 않는다. 

이번에는 아스키값에 97을 입력해 보았다.

![image](https://github.com/user-attachments/assets/b4ce0b00-a98d-4a3d-be18-6c7d94aab0b8)

'Hello admin'이 출력된다!! 4번째 짜리는 아스키 값으로 97인 'a'다!!

이어서 5번째 자리부터 8번째 자리까지 0~9까지 숫자를 입력해 줬다.

![image](https://github.com/user-attachments/assets/f907ace4-829e-4b02-a8c5-086ffbd06bfe)

![image](https://github.com/user-attachments/assets/29476550-7782-46a9-8f45-b3c5efc09b3e)

![image](https://github.com/user-attachments/assets/32832c59-b7b6-4aa0-ad4e-04c6b8ece48a)

![image](https://github.com/user-attachments/assets/16f4d18a-bbb9-4ae8-a770-1468fc69a722)

각 나리는 순서대로 '9', '8', '5', '2' 이다.

알아낸 각자리의 패스워드를 나열해 보면 '095a9852'이다. 

마지막으로 알아낸 패스워드를 입력해주면 

![image](https://github.com/user-attachments/assets/f47933a9-76a7-4bc8-9ce3-5711eccdde02)

Clear 했다!!
