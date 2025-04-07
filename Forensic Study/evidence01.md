<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] evidence01.pcap Q.1-6 Write-up</h1>
</body>
<br>
<br>
</html>

문제 설명
--
Anarchy-R-Us, Inc. suspects that one of their employees, Ann Dercover, is really a secret agent 
working for their competitor. Ann has access to the company’s prize asset, the secret recipe. Security 
staff are worried that Ann may try to leak the company’s secret recipe. 

Security staff have been monitoring Ann’s activity for some time, but haven’t found anything 
suspicious– until now. Today an unexpected laptop briefly appeared on the company wireless 
network. Staff hypothesize it may have been someone in the parking lot, because no strangers were 
seen in the building. Ann’s computer, (192.168.1.158) sent IMs over the wireless network to this 
computer. The rogue laptop disappeared shortly thereafter. 
“We have a packet capture of the activity,” said security staff, “but we can’t figure out what’s going 
on. Can you help?” 

You are the forensic investigator. Your mission is to figure out who Ann was IM-ing, what she sent, 
and recover evidence including: 

1. What is the name of Ann’s IM buddy? 
2. What was the first comment in the captured IM conversation? 
3. What is the name of the file Ann transferred? 
4. What is the magic number of the file you want to extract (first four bytes)? 
5. What was the MD5sum of the file? 
6. What is the secret recipe?
   
* MD5 (evidence.pcap) = d187d77e18c84f6d72f5845edca833f5

문제 풀이
--
**Q1. Ann 메신저 대화 상대의 이름은 무엇입니까?**

Ann의 ip주소(192.168.1.158)를 찾아보면 64.12.24.50과 통신한 것을 알 수 있다.

![image](https://github.com/user-attachments/assets/f922171a-33ee-421e-8bb6-c8cc88d26edd)

TCP strem으로 확인해보면 Sec558user1이 상대방 메신저 이름일 것 같다.

![image](https://github.com/user-attachments/assets/a8c30616-3bfe-4b4c-9d4a-7b0a1541f352)

A1. Sec558user1

**Q2. 캡처 된 IM 대화에서 첫 번째 문자는 무엇입니까?**

TCP strem으로 확인했을 때 상대방 이름 뒤에 첫 문자가 나온다.

![image](https://github.com/user-attachments/assets/bf615d12-6473-4982-9e11-616e78d12451)

A2. Here's the secret recipe. I just download it from the file server. Just copy to a thumb drive and you're good to go.

**Q3. Ann이 전송 한 파일의 이름은 무엇입니까?**

TCP strem으로 확인했을 때 recipe.docx라는 파일을 확인 할 수 있다.

![image](https://github.com/user-attachments/assets/a7d74bd5-7982-4766-9b71-f598d91fd9e2)

A3. recipe.docx

**Q4. 추출하려는 파일의 매직 넘버는 무엇입니까 (처음 4 바이트)?**

일단 recipe.docx 문자열을 가진 tcp 패킷을 검색하고 TCP strem을 보면 PK(docx 파일 시그니처)를 확인할 수 있다.

![image](https://github.com/user-attachments/assets/04957342-cac5-401f-9ff7-5a79a17703f5)

![image](https://github.com/user-attachments/assets/fc005ee7-4d54-4e99-9b7a-cd5ff26e8fe7)

A4. 50 4B 03 04

**Q5. 파일의 MD5sum은 무엇입니까?**

recipe.docx 헥스값을 복붙해서 HxD를 통해 파일을 추출했다.

![image](https://github.com/user-attachments/assets/8d7c77b0-c51d-4269-adf5-d674f2a7bd9e)

그리고 MD5sum은 어떻게 구하는지 모르겠어서 구글링을해보니 window cmd에서 구할수 있다고 한다.

```certutil -hashfile [filename] [Hashalgorithm] ```

![image](https://github.com/user-attachments/assets/0a8cae1e-4314-4f96-8e21-3603d6cc29e1)


A5. af430f89a424e8514fedbe5ad975ae4f

+좀 더 찾아보니까 아래 사이트에서도 MD5sum 을 구할 수 있다.

https://www.strerr.com/ko/md5_file.html

**Q6. 비밀 레시피는 무엇입니까?**

recipe.docx 파일을 열면 비밀 레시피를 알 수 있다.

A6.
![image](https://github.com/user-attachments/assets/233048c0-3950-4d62-8b8b-6b786d766d17)













