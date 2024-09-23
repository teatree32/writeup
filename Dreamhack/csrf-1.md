<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking csrf-1 Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 들어가 보자.
![image](https://github.com/user-attachments/assets/b1424e39-5360-437c-aea6-4ba808ebcd22)

지난주에 풀었던 xss문제들과 비교해 보았을 때 ```/notice flag``` 페이지가 새로 생겼다. 

![image](https://github.com/user-attachments/assets/49ea69ff-eeb8-41e7-9ba9-d85afd9463b5)

들어가보니 액세스 거부라고만 뜬다.

이외에는 딱히 다른게 없는것 같다. 바로 문제 파일을 다운받아보자.
```python
#!/usr/bin/python3
from flask import Flask, request, render_template
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
    return render_template("index.html")


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
        if not check_csrf(param):
            return '<script>alert("wrong??");history.go(-1);</script>'

        return '<script>alert("good");history.go(-1);</script>'


memo_text = ""


@app.route("/memo")
def memo():
    global memo_text
    text = request.args.get("memo", None)
    if text:
        memo_text += text
    return render_template("memo.html", memo=memo_text)


@app.route("/admin/notice_flag")
def admin_notice_flag():
    global memo_text
    if request.remote_addr != "127.0.0.1":
        return "Access Denied"
    if request.args.get("userid", "") != "admin":
        return "Access Denied 2"
    memo_text += f"[Notice] flag is {FLAG}\n"
    return "Ok"


app.run(host="0.0.0.0", port=8000)
```

코드를 살펴보니 ```check csrf()```함수가 눈에 띈다. 

쭉 더 읽어보니 ```/flag```페이지에서  post로 데이터를 보내면 파라미터가 실행되는 과정에서 ```domain:127.0.0.1```이 추가된다. 

아마도 이 페이지에서 명령을 보내면 127.0.0.1서버 로컬 단에 명령이 수행될것 같다. 

즉, ```/flag```에서 실행되는 명령은 ```/admin/notice_flag``` 페이지 접근 권한이 있는것 같다.

그리고 ```/flag```페이지에서 실행시킬 명령어 userid=admin 이라는 파라미터를 함께 넘겨야 하는것 같다.

위 두 조건을 기반으로 
```python
<img src="/admin/notice_flag?userid=admin"/>
```
명령어를 만들어 ```/flag```페이지에 입력했다. 
![image](https://github.com/user-attachments/assets/b81ce7df-b4f1-440d-8ff3-37898bceb12c)

![image](https://github.com/user-attachments/assets/8c7e1804-3ba8-402f-9f67-b7b25512a897)

그리고 ```/memo```페이지로 들어가니
![image](https://github.com/user-attachments/assets/5e8a33e2-60dd-4809-8cfb-1aba534c63d3)

FLAG를 얻었다!!!
