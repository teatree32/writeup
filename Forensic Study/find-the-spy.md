<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] Dreamhack find-the-spy Write-up</h1>
</body>
<br>
<br>
</html>

문제 설명
--

![image](https://github.com/user-attachments/assets/08e576a8-140a-4ed6-910d-a4bfc9a397a3)

문제 풀이
--
volatility3 사용을 위해 문제 파일 memory2.vmem, memory2.vmsn을 다운로드 받아 volatility3 도구 파일 안에 넣는다.

![image](https://github.com/user-attachments/assets/650a4372-8c50-4301-b400-881892b3f726)


우선 vmem 파일에 대해 분석 가능한 형태로 프로파일을 추출해보자
```
python vol.py -f "memory2.vmem" windows.info
```
![image](https://github.com/user-attachments/assets/36991e3a-22d2-4645-9a9b-1069ac4330c4)

본격적으로 분석을 시작해보자

zip 파일을 filescan 플러그인을 사용해서 메모리 캡처 내 확인되는 zip 파일의 가상 주소를 찾으면 된다.

실행 결과를 txt 파일로 추출해보자

```
python -X uft8 vol.py -f "memory2.vmem" windows.filescan > filescan.txt
```

![image](https://github.com/user-attachments/assets/0f64f9a8-6752-471b-9a09-15567eebee3a)


![image](https://github.com/user-attachments/assets/bf9a865e-c827-4ce6-bb88-43a740504e23)

Ctrl+F로 zip 파일을 검색해보자

![image](https://github.com/user-attachments/assets/9557749c-24f6-464b-b4b0-a83c1de4ff9f)

![image](https://github.com/user-attachments/assets/7eda8bee-8113-467d-8128-66913a8e2515)


의심되는 파일들을 찾았다. **reports.zip** 주소는 ```0xe70bd0a9f1c0```, ```0xe70bd3530b30```, 이다.

dumpfiles 플러그인을 사용해서 **reports.zip** 파일을 추출해보자

```
python vol.py -f "memory2.vmem" windows.dumpfiles --virtaddr=0xe70bd0a9f1c0 && python vol.py -f "memory2.vmem" windows.dumpfiles --virtaddr=0xe70bd3530b30

```

![image](https://github.com/user-attachments/assets/6d337a21-0c7b-4080-ad70-0619802e9dd9)

생겨난 파일을 HxD로 열어보니 PNG 헤더/푸터 시그니처를 발견했다.

![image](https://github.com/user-attachments/assets/bacd8a37-8bae-4009-a590-f11e94e928e0)

![image](https://github.com/user-attachments/assets/0a67c1c4-595d-4ec0-b393-0a15c63d1746)


해당 영역을 추출해서 사진.png로 저장해보았다.

![image](https://github.com/user-attachments/assets/2a725b49-96de-4162-bb7d-33c8dd42b533)

그리고 저장한 사진.png를 열어보니 아래와 같은 사진이 나왔다.

![image](https://github.com/user-attachments/assets/47a57066-2295-4c55-97eb-9e7b4fbb5435)

이렇게 HxD로 카빙한 결과 플래그 확인할 수 있었다.

DH{240131120000_COEX}
