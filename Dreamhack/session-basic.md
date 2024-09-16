<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking session-basic Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제 사이트로 들어가 보자.
![image](https://github.com/user-attachments/assets/2dbc3de6-174a-4b18-a273-b9a948bd22e8)

Login을 누르니 로그인 창이 나온다.
![image](https://github.com/user-attachments/assets/c860f774-375c-446c-869c-140dc2b267bd)

딱히 특별할 건 없어보이니 바로 문제파일을 다운로드 받아 열어보자
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
    'user': 'user1234',
    'admin': FLAG
}


# this is our session storage
session_storage = {
}


@app.route('/')
def index():
    session_id = request.cookies.get('sessionid', None)
    try:
        # get username from session_storage
        username = session_storage[session_id]
    except KeyError:
        return render_template('index.html')

    return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not admin"}')


@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    elif request.method == 'POST':
        username = request.form.get('username')
        password = request.form.get('password')
        try:
            # you cannot know admin's pw
            pw = users[username]
        except:
            return '<script>alert("not found user");history.go(-1);</script>'
        if pw == password:
            resp = make_response(redirect(url_for('index')) )
            session_id = os.urandom(32).hex()
            session_storage[session_id] = username
            resp.set_cookie('sessionid', session_id)
            return resp
        return '<script>alert("wrong password");history.go(-1);</script>'


@app.route('/admin')
def admin():
    # developer's note: review below commented code and uncomment it (TODO)

    #session_id = request.cookies.get('sessionid', None)
    #username = session_storage[session_id]
    #if username != 'admin':
    #    return render_template('index.html')

    return session_storage


if __name__ == '__main__':
    import os
    # create admin sessionid and save it to our storage
    # and also you cannot reveal admin's sesseionid by brute forcing!!! haha
    session_storage[os.urandom(32).hex()] = 'admin'
    print(session_storage)
    app.run(host='0.0.0.0', port=8000)
```

코드를 살펴보니 guest로 로그인이 가능할것 같다. 먼저 guest로 로그인 해보자.
![image](https://github.com/user-attachments/assets/d06ad1db-4a5c-44a9-b1c5-145488f1259d)

guest로 로그인하고 쿠키값을 확인해보니 admin의 쿠키값을 입력하면 FLAG를 얻을 수 있을 것 같다.

코드를 쭉 살펴보다 보니 admin 페이지가 따로 있고 여기서 쿠키값을 얻을 수 있을것 같다.
```python
@app.route('/admin')
def admin():
    # developer's note: review below commented code and uncomment it (TODO)

    #session_id = request.cookies.get('sessionid', None)
    #username = session_storage[session_id]
    #if username != 'admin':
    #    return render_template('index.html')

    return session_storage
```

url창에 /admin을 추가해보자.
![image](https://github.com/user-attachments/assets/baff5710-bb25-422d-96ef-115515fd598d)

admin의 쿠키값을 찾을 수 있었다. 이 쿠키값을 아까 guest 쿠키값과 바꿔주면
![image](https://github.com/user-attachments/assets/3121e64e-f4b5-4153-97e3-074bb8f5e21a)
![image](https://github.com/user-attachments/assets/a7cbc10d-1d6d-452b-8365-d1e96984a376)

FLAG를 얻을 수 있다!!!
