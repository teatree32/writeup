<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>5월 Dreamhack CTF Season 6 Round #7 Secure Image Storage Write-Up</h1>
</body>
<br>
<br>
</html>

이번 드림핵 ctf에서도 web 문제를 풀어보았다.

먼저 문제 사이트에 들어가보자.
![image](https://github.com/user-attachments/assets/ab4ce8c9-415e-4d02-ae0b-b417193b0096)

파일을 업로드할 수 있는 창이 뜬다.

이어서 문제파일을 다운로드 받아보자.

![image](https://github.com/user-attachments/assets/297171e0-d304-43e2-aa9e-00457aa1f5e9)

여러 파일들 중 app.py를 살펴보자.
```python
import sqlite3
from time import sleep
from hashlib import md5
from urllib import parse
from uuid import uuid4, UUID
from datetime import datetime
from selenium import webdriver
from os import urandom, getcwd, path, makedirs
from selenium.webdriver.chrome.service import Service
from flask import Flask, request, g, render_template, redirect, send_file, make_response

app = Flask(__name__)
app.secret_key = urandom(32)

try:
    FLAG = open('./flag.txt','r').read()
except:
    FLAG = 'DH{fake_flag}'

DATABASE = 'database.db'

def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect(DATABASE)
        db.row_factory = sqlite3.Row
    return db

@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

def init_db():
    with app.app_context():
        db = get_db()
        with app.open_resource('schema.sql', mode='r') as f:
            db.cursor().executescript(f.read())
        db.commit()

def get_data(query, args=(), one=False):
    cur = get_db().execute(query, args)
    rv = cur.fetchall()
    cur.close()
    return (rv[0] if rv else None) if one else rv

def insert_data(query, args=()):
    db = get_db()
    cur = db.cursor()
    cur.execute(query, args)
    db.commit()
    rowcount = cur.rowcount
    cur.close()
    return rowcount

def access_file(path, cookie={"name": "name", "value": "value"}):
    try:
        service = Service(executable_path="/chromedriver-linux64/chromedriver")
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
        driver.get(f"http://127.0.0.1:8000/")
        driver.add_cookie(cookie)
        driver.get(f"http://127.0.0.1:8000/{path}")
        sleep(1)
    except Exception as e:
        print(e, flush=True)
        driver.quit()
        return False
    driver.quit()
    return True

@app.route('/', methods=['GET'])
def index():
    return redirect('/upload')

@app.route('/upload', methods=['GET', 'POST'])
def upload():
    if request.method == 'GET':
        return render_template('upload.html')
    else:
        username = request.form['name'].strip()
        if len(username) > 30 :
            return render_template('upload.html', msg= 'The length of the username must be 30 characters or less')
        file = request.files['file']
        if file.filename == '':
            return redirect('/upload')
        
        if len(path.splitext(file.filename)[0]) > 30:
            return render_template('upload.html', msg= 'The length of the filename must be 30 characters or less')
        
        if ('.' in path.splitext(file.filename)[0]) or ('/' in path.splitext(file.filename)[0]):
            return render_template('upload.html', msg= 'Not allowed character')

        if path.splitext(file.filename)[1].lower() != '.png':
            return render_template('upload.html', msg= 'Only PNG files are allowed')
        
        if file.content_type != 'image/png':
            return render_template('upload.html', msg= 'Only PNG files are allowed')
        
        username = parse.quote(username, safe='')
        filename = username + '_' + parse.quote(path.splitext(file.filename)[0],safe='%')
        uuid = str(uuid4())
        base_path = getcwd()
        full_path = path.join(base_path+'/static/', uuid)
        makedirs(full_path)
        try:
            file.save(full_path + '/' + filename)
        except Exception as e:
            print(e, flush=True)
            return render_template('upload.html', msg= 'Something error')
        
        date = datetime.now().isoformat()
        encoded_data = (username+filename).encode('utf-8')
        md = md5()
        md.update(encoded_data)
        hash = md.hexdigest()
        insert_data('INSERT INTO files (username, filename, hash, uuid, date) values(?,?,?,?,?)', [username,filename,hash,uuid,date])
        return render_template('upload.html', msg='Upload success')


@app.route('/<username>', methods=['GET','POST'])
def storage(username):
    files=get_data('SELECT * FROM files WHERE filename LIKE ?',[username+'_%'])
    return render_template('storage.html', files=files)


@app.route('/static/<uuid>/<filename>')
def show(uuid, filename):
    uuid_obj = UUID(uuid, version=4)
    if str(uuid_obj) != uuid:
        return 'Not vaild UUID'
    file = get_data('SELECT * FROM files WHERE uuid=?',[uuid], one=True)
    if not file:
        return 'Directory does not exist'
    base_path = getcwd()
    if path.exists(base_path+f'/static/{uuid}/{filename}'):
        res = make_response(send_file(base_path+f'/static/{uuid}/{filename}'))
        # res.headers.add('X-Content-Type-Options', 'nosniff')
        res.headers.add('Content-Type', 'image/png')
        res.headers.add(f'X-Confirmed-{parse.unquote(filename)}', parse.unquote(file[1]) +'_' + file[3])
        for header in res.headers.to_wsgi_list():
            if 'Content-Type' in header[0]:
                if (not header[1].startswith('image/png')) and (not header[1].startswith('application/octet-stream')):
                    return 'Invaild Content-Type'
        return res
    else: 
        return 'File does not exist'


@app.route("/report", methods=["GET", "POST"])
def report():
    if request.method == "POST":
        path = request.form.get("path")
        if not path:
            return render_template("report.html", msg="fail")
        else:
            if access_file(path, cookie={"name": "flag", "value": FLAG}):
                return render_template("report.html", msg="Success")
            else:
                return render_template("report.html", msg="fail")
    else:
        return render_template("report.html")


if __name__ == "__main__":
    init_db()
    app.run(host='0.0.0.0', port=8000)
```

코드들을 쭉 살펴보니 flag를 얻으려면 flag.txt 파일을 열어야 할 것 같다. 그리고 png 파일만 업로드가 가능한것 같은데....

아직 web문제에 대한 지식이 풍부한 편은 아니지만 짐작으로는 webshell 문제인것 같다. 그런데 문제는 php 파일은 업로드가 불가능하다는 건데....

그래서 구글링을 통해 우회방법을 몇개 찾아보았다.

먼저 첫번째는 nul byte, 주석으로 2중 확장자 넘기기(ex. shell.php%00.png, shell.php#.png)인데 이건
```python
if ('.' in path.splitext(file.filename)[0]) or ('/' in path.splitext(file.filename)[0]):
            return render_template('upload.html', msg= 'Not allowed character')
```
요 코드때문에 안되고

두번째로 확장자명을 대소문자 변경하는 것도 확장자가 png 인것만 받으니깐 불가능하다.

세번째로 shell파일 맨 앞에 이미지 파일 시그니쳐를 추가하는 것은 시도해보았다.
업로드는 성공적으로 되지만 서버가 png파일로 인식하여 php 파일이 실행되지 않는다.

그리고 마지막으로 burpsuite를 사용해서 content-type 우회하기도 사용해 보았으나 세번째와 마찬가지로 파일업로드는 되나 
![스크린샷 2024-09-12 004044](https://github.com/user-attachments/assets/fb53e0f8-19b0-43b5-bf7a-2374e63b85a8)

서버가 png 파일로 인식하여 php 파일이 실행되지 않는다.
![스크린샷 2024-09-12 004217](https://github.com/user-attachments/assets/3143e615-9d3e-46e0-aacd-5f537ce77ce0)
![image](https://github.com/user-attachments/assets/ce2945e3-47e2-4e5f-be5d-1435bf82d87e)

여러 방법으로 우회할 방법을 찾아보았지만 잘 되지 않았다. 이러면 webshell 문제가 아닌것 같은데 그렇다면 어떻게 풀어야 할지 전혀 감이 오지않는다....
