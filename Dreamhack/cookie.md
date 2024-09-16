<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking cookie Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제 사이트로 들어가 보자.
![image](https://github.com/user-attachments/assets/25cdabde-acca-4930-90be-215242d47731)

login을 눌러보니 로그인 창이 뜬다. 
![image](https://github.com/user-attachments/assets/eb140705-c9df-46fa-ab2d-7bcd1315a359)

딱히 더 특별한건 없어보이니 바로 문제 파일을 다운로드 받아 열어보자.
```python
#!/usr/bin/python3
from flask import Flask, request, render_template, make_response, redirect, url_for

app = Flask(__name__)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

users = {
    'guest': 'guest',
    'admin': FLAG
}

@app.route('/')
def index():
    username = request.cookies.get('username', None)
    if username:
        return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not admin"}')
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        try:
            pw = users[username]
        except:
            return '<script>alert("not found user");history.go(-1);</script>'
        if pw == password:
            resp = make_response(redirect(url_for('index')) )
            resp.set_cookie('username', username)
            return resp 
        return '<script>alert("wrong password");history.go(-1);</script>'

app.run(host='0.0.0.0', port=8000)
```

코드를 살펴보니 guest로 로그인이 가능하고 username이 admin일때 password가 플래그 인것 같다.

먼저 guest로 로그인 해보자.
![image](https://github.com/user-attachments/assets/910a6c25-88f1-4b34-9033-650e3d0097b2)
![image](https://github.com/user-attachments/assets/1f4006fa-dd3f-4f17-83eb-53af76409dea)

개발자 도구를 열어 쿠키를 확인해보자.
![image](https://github.com/user-attachments/assets/b37fc993-f117-4e35-8ca3-5302a7b4aa01)

쿠키값이 guest인것을 확인 할 수 있는데 이 값을 admin으로 바구고 새로고침하면 FLAG를 얻을 수 있을 것 같다.
![image](https://github.com/user-attachments/assets/d3b91601-0dcb-4344-a451-4b00ab96a49f)
![image](https://github.com/user-attachments/assets/0e7b9250-d081-44c3-aa46-a00af3c216a5)

FLAG를 찾았다!!!
