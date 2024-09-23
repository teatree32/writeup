<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking csrf-2 Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 들어가 보자.
![image](https://github.com/user-attachments/assets/21ae30cc-0e17-401e-8142-34f44fa67e34)

아주 크게 로그인하라고 써있다. 
admin으로 로그인 해보았다.

![image](https://github.com/user-attachments/assets/792cff23-d1c5-4f7f-b0b2-4ae6bd0b15f2)
![image](https://github.com/user-attachments/assets/42432d23-ef45-487e-83e6-cce823314c5e)

password가 틀리다고 나온다. 이어서 guest로 해보았다.
![image](https://github.com/user-attachments/assets/6ce0c345-bdb9-4ac2-ab91-2855cbb89636)
![image](https://github.com/user-attachments/assets/1c385cbe-f005-4697-8b3b-b2c28d5ee3b0)

guest로는 로그인이 된다. 바로 문제 파일을 다운받아 봤다.

```python
#!/usr/bin/python3
from flask import Flask, request, render_template, make_response, redirect, url_for
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
import urllib
import os

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open("./flag.txt", "r").read()
except:
    FLAG = "[**FLAG**]"

users = {
    'guest': 'guest',
    'admin': FLAG
}

session_storage = {}

def read_url(url, cookie={"name": "name", "value": "value"}):
    cookie.update({"domain": "127.0.0.1"})
    try:
        service = Service(executable_path="/chromedriver")
        options = webdriver.ChromeOptions()
        for _ in [
            "headless",
            "window-size=1920x1080",
            "disable-gpu",
            "no-sandbox",
            "disable-dev-shm-usage",
        ]:
            options.add_argument(_)
        driver = webdriver.Chrome(service=service, options=options)
        driver.implicitly_wait(3)
        driver.set_page_load_timeout(3)
        driver.get("http://127.0.0.1:8000/")
        driver.add_cookie(cookie)
        driver.get(url)
    except Exception as e:
        driver.quit()
        print(str(e))
        # return str(e)
        return False
    driver.quit()
    return True


def check_csrf(param, cookie={"name": "name", "value": "value"}):
    url = f"http://127.0.0.1:8000/vuln?param={urllib.parse.quote(param)}"
    return read_url(url, cookie)


@app.route("/")
def index():
    session_id = request.cookies.get('sessionid', None)
    try:
        username = session_storage[session_id]
    except KeyError:
        return render_template('index.html', text='please login')

    return render_template('index.html', text=f'Hello {username}, {"flag is " + FLAG if username == "admin" else "you are not an admin"}')


@app.route("/vuln")
def vuln():
    param = request.args.get("param", "").lower()
    xss_filter = ["frame", "script", "on"]
    for _ in xss_filter:
        param = param.replace(_, "*")
    return param


@app.route("/flag", methods=["GET", "POST"])
def flag():
    if request.method == "GET":
        return render_template("flag.html")
    elif request.method == "POST":
        param = request.form.get("param", "")
        session_id = os.urandom(16).hex()
        session_storage[session_id] = 'admin'
        if not check_csrf(param, {"name":"sessionid", "value": session_id}):
            return '<script>alert("wrong??");history.go(-1);</script>'

        return '<script>alert("good");history.go(-1);</script>'


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
            session_id = os.urandom(8).hex()
            session_storage[session_id] = username
            resp.set_cookie('sessionid', session_id)
            return resp 
        return '<script>alert("wrong password");history.go(-1);</script>'


@app.route("/change_password")
def change_password():
    pw = request.args.get("pw", "")
    session_id = request.cookies.get('sessionid', None)
    try:
        username = session_storage[session_id]
    except KeyError:
        return render_template('index.html', text='please login')

    users[username] = pw
    return 'Done'

app.run(host="0.0.0.0", port=8000)
```

코드를 쭉 살펴보니 비밀번호가 일치하면 세션ID를 생성하고 seeion_strage에 사용자 이름과 매핑해서 저장하는것 같다.

그럼 admin으로 접속하면 flag를 얻을 수 있을것 같다.

admin으로 로그인하려면 password를 바꿔줘야 할 것 같은데 ```/change_password```를 보면 password를 ```pw```파라미터에 담김 값으로 변경한다.

아래와 같이 명령어를 만들어 보았다.
```python
<img src="/change_password?pw=1234"/>
```

```/flag```페이지에 입력해 주었다.

![image](https://github.com/user-attachments/assets/ebd559ee-5566-4d1c-98f5-2916a7428952)

![image](https://github.com/user-attachments/assets/4444230f-ca7d-46e6-bdbb-48e48e5cfd5d)

그리고 로그인 페이지로가 username은 admin으로 password는 1234로 입력해보았다.
![image](https://github.com/user-attachments/assets/86d34baa-8552-4d84-a7e3-737db57ea1f9)

![image](https://github.com/user-attachments/assets/3b5c17bd-83a4-40f9-95fd-24303cee0dea)

FLAG를 얻었다!!!
