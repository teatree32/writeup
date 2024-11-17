<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>Block CTF 2024 #Reversing Nothin_But_Stringz Write-Up</h1>
</body>
<br>
<br>
</html>

![image](https://github.com/user-attachments/assets/a25fa746-d393-4ff5-8b52-7fc5876595d4)

먼저 문제를 살펴보면 head와 taildmf 못 찾겠으니 도와달라고 한다.

우선 주어진 파일을 다운받아서 HexEditor로 열어 보았다.
![image](https://github.com/user-attachments/assets/283acff0-07cc-4ff8-b1c3-9c7cbcbac98f)

우선 확장자를 먼저 확인해보았는데, DE C0에 해당하는 확장자가 명쾌하게 존재하지는 않는 것 같다. 

마지막 부분을 보니 C언어랑 유사한 코드가 있어서 이 부분이 단서가 되지 않을까 생각했다. 
![image](https://github.com/user-attachments/assets/1488eb84-e04c-490b-89f1-c615e9dc903e)

예전에 파일 카빙을 하면서 압축을 하거나 압축을 풀면 단서가 나왔던 것이 떠올라 압축을 해제해보려고 7-Zip을 다운 받아 실행해보았다. 하지만 압축 해제 대상이 아니라고 뜬다. 

HexEditor로 연 파일을 노트북 바탕화면에 다운 받아 보니 확장자가 ```.c.o```이다. 

확장자 ```.c.o```는 컴파일된 오브젝트 파일을 나타낸다고 한다. 그래서 ```.c.o```에서 ```.c```로 돌아가는 방법을 찾으면 되나 했는데, 검색해보니 그 방향으로 불가능하다고 뜬다. 

디스어셈블리를 통해 기계어 코드를 어셈블리 언어로 변환할 수 있다고 하여 리눅스에서 명령어를 실행해보았다. 

![image](https://github.com/user-attachments/assets/31c0359e-904e-4e39-8748-f8edce918db6)

출력을 보니 file format not recognized 라고 뜬다. 

그래서 file 명령어를 통해 파일의 형식을 찾아봤다. 

![image](https://github.com/user-attachments/assets/d82454a2-d619-485b-9974-770f22ca26a6)

LLVM bitcode, wrapper라고 한다. 

확장자는 일반적으로 ```.bc```라고 하여 기존의 확장자였던 ```.c.o```를 ```.bc```로 바꾸어보았다. 

바꾼 뒤, llvm-dis 명령어를 통해 Bitcode 파일을 사람이 읽을 수 있는 IR 텍스트 형식으로 바꾸어 보았다. 

![image](https://github.com/user-attachments/assets/24b145df-ab1d-4176-87ee-27fb097cd145)

IR 텍스트 형태로 바꾼 파일을 file.ll이라고 저장하고 cat 명령어를 통해서 열어보았다. 

![image](https://github.com/user-attachments/assets/e8dcce35-0b39-464e-94a2-17847ff78f59)

열어보니 flag 값을 찾을 수 있었다!!

flag{al1_th3_h0miez_l0v3_llvm_643e5f4a}
