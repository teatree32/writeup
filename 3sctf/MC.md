<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #PWN MC Write-Up</h1>
</body>
<br>
<br>
</html>

우선 문제파일을 다운로드 받아 압축을 풀어보았다.
![image](https://github.com/user-attachments/assets/a32eab19-8ed9-4025-b8a5-0be3a1176cd2)

파일이 여러개 있는데 그중 열어서 해석해볼수 있는 파일이 c파일이라서 우선 c파일을 열어 코드를 해석해 보았다.

우선 전체적으로 보면 pthread 라이브러리를 사용하여 스레드를 생성하고, 사용자가 입력한 데이터를 스레드에서 읽어 처리하는 프로그램인 것 같다.

하나하나 자세히 보자

`giveshell()`

이 함수는 /bin/sh를 실행하여 쉘을 제공한다고 한다.

`init()`

표준 입력(`stdin`)과 표준 출력(`stdout`)의 버퍼링 방식을 설정하고 `setvbuf()` 함수 호출을 통해 각각의 버퍼링 모드를 설정한다.

`stdin`과 `stdout`을 버퍼링 모드 2로 설정한다.

`read_bytes(char *buf, int size)`

사용자가 입력하는 데이터를 읽어 `buf` 버퍼에 저장하고 데이터를 8바이트씩 읽고 `buf`에 저장한다.

`read()` 함수 호출이 8바이트를 읽지 못하면 함수가 반환된다.

`thread_routine()`

스레드가 실행할 함수다. 사용자에게 `Size`를 입력받아 size 변수에 저장한 후, `read_bytes()` 함수를 호출하여 데이터를 읽는다.

입력된 size는 실제로 읽을 데이터의 크기를 나타내지만 `size` 값에 따라 버퍼 오버플로우가 발생할 수 있다.

`main()`

스레드를 생성하고 실행하는 메인 함수다.

`pthread_create()`를 호출하여 `thread_routine` 함수를 실행하는 스레드를 생성고 `pthread_join()`을 호출하여 생성된 스레드가 종료될 때까지 기다린다.

일단 이렇게 코드를 분석해 보기했는데 이 외에는 어떻게 해야할지 감이 잡히지 않는다....
