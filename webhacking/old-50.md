<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Webhacking old-50 Write-Up</h1>
</body>
<br>
<br>
</html>

문제 서버로 들어가보자.
![image](https://github.com/user-attachments/assets/71272d13-92f2-4109-8066-9941665db0a7)

id와 pw를 입력할 수 있는 입려칸이 있다.

소스 코드를 살펴보자
```python
<?php
  include "../../config.php";
  if($_GET['view_source']) view_source();
?><html>
<head>
<title>Challenge 50</title>
</head>
<body>
<h1>SQL INJECTION</h1>
<form method=get>
id : <input name=id value='guest'><br>
pw : <input name=pw value='guest'><br>
<input type=submit>&nbsp;&nbsp;&nbsp;<input type=reset>
</form>
<?php
  if($_GET['id'] && $_GET['pw']){
    $db = dbconnect();
    $_GET['id'] = addslashes($_GET['id']); 
    $_GET['pw'] = addslashes($_GET['pw']);
    $_GET['id'] = mb_convert_encoding($_GET['id'],'utf-8','euc-kr');
    foreach($_GET as $ck) if(preg_match("/from|pw|\(|\)| |%|=|>|</i",$ck)) exit();
    if(preg_match("/union/i",$_GET['id'])) exit();
    $result = mysqli_fetch_array(mysqli_query($db,"select lv from chall50 where id='{$_GET['id']}' and pw=md5('{$_GET['pw']}')"));
    if($result){
      if($result['lv']==1) echo("level : 1<br><br>");
      if($result['lv']==2) echo("level : 2<br><br>");
    } 
    if($result['lv']=="3") solve(50);
    if(!$result) echo("Wrong");
  }
?>
<hr><a href=./?view_source=1>view-source</a>
</body>
</html>
```

코드를 살펴보면 필터링이 많이 되고있는 것을 확인할 수 있는데

id와 pw값에 ```addslashes()```함수가 적용되어 있고

```mb_convert_encoding()```함수로 id값에 대해 euc-kr을 utf-8형식으로 인코딩하게 되어있다.

그리고 ```preg_match()```함수를 사용해서 ```from```, ```pw```, ```(```, ```)```, ```공백```, ```%```, ```=```, ```>```, ```<```을 필터링 하고 있다.

또한, id값에 대해 union 문자열을 대소문자 구별 없이 필터링하고 있는것을 알 수 있다.

이어서 쿼리문을 보면 
```python
select lv from chall50 where id='{$_GET['id']}' and pw=md5('{$_GET['pw']}')
```
로 되어있다.

문제를 풀기 위해서는 lv값이 3이되게 하면 될것 같다. 

문제를 풀기위해 ```addslahses()```와 ```mb_convert_encoding()``` 이 정확히 무엇인지 찾아보았다.

```addslashes()```는 php에서 따옴표(싱글쿼터)를 처리해주는 함수로 이스케이프할 문제앞에 백슬래시(\)처리해주는 함수라고 한다.

```mb_convert_encoding()```은 문자열을 한 인코딩에서 다른 인코딩으로 변환해주는 함수라고 한다.

이들을 우회하기 위해서는 멀티바이트 문자를 삽입하면 된다.

mb_convert_encoding() 함수는 멀티바이트를 사용하는 언어셋 환경에서 \ (백슬래시) 앞에 %a1~%fe 값이 들어오면 인코딩이 깨지며 \ (백슬래시)를 덮어버리게 되는 문제가 발생한다.

멀티바이트 문자(%a1~%fe)를 사용하는 utf-8 환경에서 %a1~%fe 값과 백슬래시에 해당하는 %5c가 합쳐져서 하나의 문자를 나타내게 된다.

즉, ```addslahses()``` 함수로 인해 삽입된 백슬래시앞에 의도적으로 멀티 바이트 문자를 삽입하면 백슬래시를 하나의 문자로 인식하여 무효화되어 우회가 가능하다.

쿼리문을 우회하기 위해 스페이스바를 /**/로 대체하였고

```mb_convert_encoding()``` 함수의 취약점을 활용하여 ' (single quote) 를 위해 %a1%27 를 입력으로 주었다. 

md5 암호화 하는 부분 또한 삭제하기 위해 /**/ (주석)을 사용하였다. 

%27은 ' (single quote) 를 URL Encoding 하면 나오는 값으로 ```addslashes()``` 함수로 인해 \' 이와 같이 표현된다.

그렇기에, \ (백슬래시)를 없애주기 위해 %a1를 ```붙여 mb_convert_encoding()``` 함수의 취약점을 활용해 주었다.

```python
 select lv from chall50 where id='%a1%27/*' and pw=md5('*/union/**/select/**/3#')
```
위와 같은 쿼리문이 되도록 우회문자로 바꿔서 입력해주면
```python
?id=%a1%27/*&pw=*/union/**/select/**/3%23
```

![image](https://github.com/user-attachments/assets/73e9c027-6daa-4d95-9619-c510959404fe)
![image](https://github.com/user-attachments/assets/bb48ff61-855f-4210-a089-ab45b4d3ab7f)

문제가 풀렸다!!

