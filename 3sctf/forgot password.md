<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #WEB Forgot password? Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제 사이트에 들어가보니 로그인 창이 뜬다.
![image](https://github.com/user-attachments/assets/3a6253e3-5cc9-4d63-ad18-87b40aac9f16)

문제 파일을 다운받아 app.py 들어가 보았다.
코드를 쭉 훑어보니 마지막에 FLAG를 얻을 수 있는 코드가 보인다.
```python
@app.route("/")
def flag_page(FLAG="3S{FAKE FLAG}"):
    if "user" in session:
        if session["user"] == "admin@3sctf.admin.com":
            FLAG=os.getenv("FLAG")
        return render_template("flag.html", FLAG=FLAG)
    else:
        return redirect("/login")
```

admin@3sctf.admin.com으로 로그인 하면 FLAG를 얻을 수 있을 것 같다.

Users 리스트를 살펴보니 관리자 계정이 설정되어있다.
```python
Users = [{"username":"admin", 
          "email":"admin@3sctf.admin.com", 
          "password":os.urandom(32)},]
```
비밀번호가 랜덤으로 생성되는것을 확인할 수 있다.

이어서 로그인코드를 살펴보자.
```python
@app.route("/login", methods=["GET", "POST"])
def login_page():
    if "user" not in session:
        if request.method == "GET":
            return render_template("login.html")
        else:
            try:
                email = request.form["user_email"]
                pwd = request.form["password"]
                user = list(filter(lambda user : user['email'] == email and user['password'] == pwd, Users))
                if user:
                    if user[0]["email"]=="admin@3sctf.admin.com":
                        admin_info = Users[0]
                        admin_info["password"] = os.urandom(32)
                        Users[0] = admin_info
                    session["user"] = user[0]["email"]
                    return redirect("/")
                else:
                    flash("Invalid email/password combination")
                    return render_template("login.html")
            except Exception as e:
                flash("An error occured")
                return render_template("login.html")
    else:
        return render_template_string("<script>alert('Log out first');location.href='/';</script>")
```
로그인 페이지 코드를 살펴보니 비밀번호가 관리자로 로그인 할 때 마다 랜덤으로 바뀌는 것 같다. 

그럼 관리자로 로그인 하기 위해서 비밀번호를 고정하고 로그인 할때 마다 비밀번호가 바뀌지 않도록 코드를 수정하면 될것 같다고 생각했다.
그래서 우선 Users 리스트에서 비밀번호를 고정하고
```python
Users = [{"username":"admin", 
          "email":"admin@3sctf.admin.com", 
          "password":"fixed_admin_password"},]
```
관리자로 로그인할때 비밀번호를 랜덤으로 바꾸는 코드를 주석처리 했다.
```python
@app.route("/login", methods=["GET", "POST"])
def login_page():
    if "user" not in session:
        if request.method == "GET":
            return render_template("login.html")
        else:
            try:
                email = request.form["user_email"]
                pwd = request.form["password"]
                user = list(filter(lambda user : user['email'] == email and user['password'] == pwd, Users))
                if user:
                    #if user[0]["email"]=="admin@3sctf.admin.com":
                        #admin_info = Users[0]
                        #admin_info["password"] = os.urandom(32)
                        #Users[0] = admin_info
                    session["user"] = user[0]["email"]
                    return redirect("/")
                else:
                    flash("Invalid email/password combination")
                    return render_template("login.html")
            except Exception as e:
                flash("An error occured")
                return render_template("login.html")
    else:
        return render_template_string("<script>alert('Log out first');location.href='/';</script>")
```

코드 수정후 다시 로그인 창으로 들어가 관리자로 로그인을 시도해보자
![스크린샷 2024-07-27 115833](https://github.com/user-attachments/assets/1233fffd-85f4-4d25-a0b5-d4c8caf1add3)

Sign in을 누르니

![스크린샷 2024-07-27 115814](https://github.com/user-attachments/assets/06726a06-34bd-45ef-b3b5-95299f72fc15)

FLAG 창이 떳다.
그런데 뭔가 이상하다 None이라고 뜨고 FLAG가 나타나지 않는다.
일단 매우 당황스럽지만 다른 방법으로 관리자 계정에 접근 하는 방법을 생각해 봐야겠다.

