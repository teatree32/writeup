<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] shark Write-up</h1>
</body>
<br>
<br>
</html>

문제 파일 shark.pcapng를 Wireshark로 열어보자

FTP 로 Secret.png와 thumb_layer7.png를 저장하는게 눈에 띈다.

![image](https://github.com/user-attachments/assets/e77b9835-4da9-4d8a-aabb-9223cb9c73cf)

![image](https://github.com/user-attachments/assets/17753b87-4160-44cf-b968-4806e49bf5bb)

그래서 TCP stream으로 열어서 png 파일 헥스값을 HxD를 통해 저장하였다.

![image](https://github.com/user-attachments/assets/4db7f82a-ee56-4712-bf16-2bea47f1db96)

![image](https://github.com/user-attachments/assets/0b44680c-1616-456b-b73b-464c8deec86a)

저장된 png 파일들을 각각 열어보면 다음과 같다.

secret.png
![secret](https://github.com/user-attachments/assets/31cfb38f-1ea1-4d96-800c-3dbf5e765ebb)

thumb_layer7.png
![thumb_layer7](https://github.com/user-attachments/assets/ae001433-93af-46f6-906c-755b55364793)

일단 딱 봤을 때 secret.png 파일이 이상하다

확대해서 보면 뭔가 사진 중간 쯤 부터 눌러논 듯하게 생겼다. 

![image](https://github.com/user-attachments/assets/346e0f30-5c71-4970-8ae9-7beac97ee7a5)

사진 크기에 대한 문제라고 생각해서 CRC 값을 통해 실제 사진 너비와 높이를 측정해보고 측정된 값으로 사진 크기를 바꾸면 될것 같다.

우선 현 사진 크기는 3150 x 524 이다.

![image](https://github.com/user-attachments/assets/66f434bc-5377-4115-9028-661b9b884288)

CRC 를 구하는 드는 다음과 같다.

```python
from zlib import crc32

file_path = r"C:\Users\ADMINCOM\OneDrive - 서울여자대학교\바탕 화면\Secret.png"

with open(file_path, 'rb') as f:
    data = f.read()

# 8바이트 PNG signature 다음에 오는 IHDR 청크
ihdr_offset = 8
length = int.from_bytes(data[ihdr_offset:ihdr_offset + 4], 'big')  # 보통 13
chunk_type = data[ihdr_offset + 4:ihdr_offset + 8]  # b'IHDR'
chunk_data = data[ihdr_offset + 8:ihdr_offset + 8 + length]
crc_read = int.from_bytes(data[ihdr_offset + 8 + length:ihdr_offset + 8 + length + 4], 'big')

print("Read CRC from file:", hex(crc_read))

# 우리가 계산해보자
crc_calc = crc32(chunk_type + chunk_data)
print("Calculated CRC    :", hex(crc_calc))

# width, height 출력
width = int.from_bytes(chunk_data[0:4], 'big')
height = int.from_bytes(chunk_data[4:8], 'big')
print(f"Original width={width}, height={height}")

```

실행해보자.

![image](https://github.com/user-attachments/assets/9a42c6ba-5a89-466c-baac-cdd5037ceab5)

어라?? 뭐지 출력값이 3150 x 524 다??

이러면 안되는데 원래 사진 사이즈와 똑같다...

당연히 사진 크기 문제일 줄 알았는데...

그래도 혹시모르니 정사각형 모양으로 높이를 3150으로 바꿔보았다.

![image](https://github.com/user-attachments/assets/dcb10d41-273a-4688-8ba6-2eea30660f11)

HxD로 높이 값을 바꿔 파일을 열어보았다.

![image](https://github.com/user-attachments/assets/b35643e2-d294-4445-9993-3d6b44aaa2ca)

그냥 하얀 부분만 늘어나고 구겨진 듯한 글자는 펴지지 않았다....

아 진짜 모르겠다....
