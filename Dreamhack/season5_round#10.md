<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>5월Dreamhack CTF Season 5 Round #10 pharmacy Write-Up</h1>
</body>
<br>
<br>
</html>

이번 CTF에서도 Web 문제인 Dreamhack CTF Season 5 Round #10 (🌱Div2)에 pharmacy를 풀어보았다.
![image](https://github.com/teatree32/writeup/assets/164837312/48715c36-a1bf-4414-a1e9-0fccfca4fa5b)

우선 문제를 다운받고 압축을 풀어보자.
![image](https://github.com/teatree32/writeup/assets/164837312/4d1bf3d9-7f3c-405d-a03f-a056987dc30a)

일단 파일들을 열어보면 형태는 위와 같다. 각각의 파일들이 무엇인지 생각해보자.

Dockerfile : 이미지를 생성하는 파일이다.

deploy/run.sh : 플레그가 나타나는 쉘 스크립트 파일인것 같다.

deploy/flag : 텍스트 문서인데 정확히 뭔지는 모르겠지만 flag를 얻는데 필요한 파일 같다.

deploy/src/supermarket : 코드를 보면 특정 작업이 끝나면 실행되는 파일같다.

deploy/src/style : 실제 웹 사이트에서 보이는 스타일 시트 파일이다.

deploy/src/index : parmacy 웹 사이트를 나타내고 있는 파일로 처방전 이미지를 업로드할 수 있도록 코드가 만들어져 있는것 같다.

deploy/src/uploads/placeholder : 이미지를 업로드할 때 필요한 파일 같다.

그럼 파일을 하나하나 분석해 보자. 먼저 Dockerfile 파일 코드를 해석해보겠다.
```python
FROM php:7.4-apache

COPY ./deploy/run.sh /usr/sbin/
RUN chmod +x /usr/sbin/run.sh

COPY ./deploy/src /var/www/html
RUN chmod 777 /var/www/html/uploads

COPY ./deploy/flag.txt /

EXPOSE 80
CMD ["/usr/sbin/run.sh"]
```

4월달 CTF에서 공부해본 내용과 겹치는 내용이 있지만 chmod 777 은 처음 보는 코드라서 검색해 보았다.
파일 사용 권한은 숫자 또는 기호 형식으로 나타낼 수 있는데 r(읽기) = 4, w(쓰기) = 2, x(숫자) = 1을 의미한다. 이때 7은 4+2+1로 읽기 쓰기 및 실행권한을 가지는 것을 의미한다.

풀이 : php:7.3-apache 이미지에서 시작해 ./deploy/run.sh/usr/svin/ 경로에 있는 파일을 가져오고 새로운 레이어에서 /usr/sbin/run.sh 실행권한을 추가한다. 그리고 ./deploy/src/var/www/html 파일을 가져오고 새로운 레이어에서 /var/www/html/uploads 읽기 쓰기 및 실행권한을 추가한다. 이어서 80포트로 이미지를 열고 /usr/sbin/run.sh을 실행한다.

이어서 run.sh을 살펴보자.
```python
#!/bin/bash

export FLAG="test"
&>/dev/null /usr/sbin/apachectl -DFOREGROUND -k start
```

4월달 CTF에서 본 run.sh와 같은 것을 알 수 있는데 아직도 export 명령어를 통해 지역변수를 전역변수로 만드는 이유는 잘 모르겠다.

다음으로 flag 파일을 살펴보자.
```python
placeholder
```

그냥 텍스트 파일로 placeholder 라고 적혀있다. 무슨 뜻이 있을까 싶어 검색애 보았다.
HTML의 placeholder 속성은 사용자가 어떤 것을 입력해야 할지 안내해 주고 입력하면 사라지는 힌트를 표시할 때 사용하는 속성으로 밑에와 같은 형태로 사용된다.
```python
<input type="text" placeholder="내용을 입력하세요.">
```

하지만 flag 파일에는 placeholder 라고만 쓰여있어서 정확히 위와 같은 뜻을 가지는지는 잘 모르겠다.

이어서 supermarket 파일을 살펴보자.
```python
<?php
function goodbye($customer) {
    echo "Good bye, $customer!\n";
}

class Supermarket {
    public $greet = 'goodbye';
    public $customer = 'dream';
    function __destruct() {
        call_user_func($this->greet, $this->customer);
    }
}
?>
```

코드를 살펴보면 goodbye 함수를 만들고 Supermarket 클래스를 만든것을 확인할 수 있다. 무슨 용도인지는 정확히 모르겠지만 index 파일에서 용하려고 만들어진것 같다고 추측해본다.

다음으로 style파일을 살펴보자.
```python
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    padding: 20px;
    text-align: center;
}

.file-upload-form {
    background-color: #fff;
    padding: 20px;
    border-radius: 8px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    max-width: 500px;
    margin: auto;
}

.file-upload-form h2 {
    text-align: center;
    color: #333;
}

.file-upload-input {
    border: 1px solid #ddd;
    padding: 10px;
    border-radius: 4px;
    width: 75%;
    margin-bottom: 15px;
}

.file-upload-button {
    background-color: #4CAF50;
    color: white;
    padding: 10px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    width: 100%;
    font-size: 16px;
}

.file-upload-button:hover {
    background-color: #45a049;
}
```


index파일의 스타일을 나타내는 css 파일임을 알 수 있다.

이어서 index 파일을 살펴보자.
```python
<?php
require("supermarket.php");
?>

<!DOCTYPE html>
<html>
<head>
    <link rel="stylesheet" type="text/css" href="style.css">
    <title>Pharmacy</title>
</head>
<body>
    <h1>Pharmacy💊</h1>
    <h2>Upload your prescription in gif format...</h2>
    <form action="index.php" method="post" enctype="multipart/form-data" class="file-upload-form">
        <input type="file" name="fileToUpload" id="fileToUpload" class="file-upload-input">
        <input type="submit" value="upload" name="submit" class="file-upload-button">
    </form>
    <br>

<?php
$targetDirectory = "uploads/";
$uploadOK = 1;

if( isset($_POST["submit"])) {
    echo '<pre class="file-upload-form">';
    $tmpFile = $_FILES["fileToUpload"]["tmp_name"];
    $currentFile = $_FILES["fileToUpload"]["name"];
    $fileExtension = strtolower(pathinfo($currentFile, PATHINFO_EXTENSION));

    if (mime_content_type($tmpFile) !== "image/gif" || $fileExtension !== "gif") {
        echo "Prescription not gif!\n";
        $uploadOK = 0;
    }

    if ($uploadOK == 0) {
        echo "Prescription upload failed.\n";
    } else {
        $randomFileName = bin2hex(random_bytes(16));
        $targetFile = $targetDirectory . $randomFileName . "." . $fileExtension;
        if (move_uploaded_file($tmpFile, $targetFile)) {
            if (isset($_POST['emergent']))
                $targetFile = 'phar://' . $targetFile;
            else
                $targetFile = $targetFile;

            if (file_exists($targetFile)) {
                echo "Prescription submitted!\n";
            }
        } else {
            echo "Prescription upload failed.\n";
        }
    }
    echo '</pre>';
}
?>

</body>
</html>
```

코드를 살펴보면 supermarke.php와 style.css가 연결된 것을 확인할 수 있다. 그외에는 잘 모르겠다....

그래도 뭔가 해볼 수 있는게 없을까 싶어 문제 서버로 들어갔다.
![image](https://github.com/teatree32/writeup/assets/164837312/aa044bd3-7708-43f4-b85c-cfbfcccb2059)

회장님께서 알려주신대로 BurpSuite를 사용해봤다.
![image](https://github.com/teatree32/writeup/assets/164837312/4416130e-88e6-4156-ba6f-b38bc5c4e6d4)

BurpSuite를 켜고 웹 이것저것을 눌러보니 뭔가 값들이 들어오지만 정확히 뭘 의미하는지 잘 모르겠다. 이것들을 보고 문제를 어떻게 풀어나가야 할지 감이 전혀 잡히지않는다. 이번 CTF는 여기까지인것 같다. 4월달 CTF와 비슷한 부분들이 있었지만 새롭게 알게된것들도 많아 앞으로 더 많이 공부해야겠다고 생각했다. 남은 풀이는 다른 학회원분들이 푼 writeup을 참고해 더 공부하고자 한다.
