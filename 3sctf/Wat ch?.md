<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>3SCTF #Forensic Wat ch? Write-Up</h1>
</body>
<br>
<br>
</html>

문제를 다운받으면서 확인해보니
1. 어플리케이션 개수
2. 3번째 리마인더 작성 시간(HH:MM:SS)
3. 음악 파일명(@@@.mp3)
4. 김소리의 거주지(countryName cityName)
5. 1695650088에 뉴스를 발행한 author
이렇게 5개를 찾으면 될것 같다.

다운로드 받은 파일의 압축을 풀고 하나하나 찾기 시작했다.

먼저 2번 3번째 리마인더 작성 시간을 reminderConsumerLog에서 찾을 수 있었다.

![image](https://github.com/user-attachments/assets/71407898-249a-47fe-93fa-7cd2689e0468)

![image](https://github.com/user-attachments/assets/5c57f810-a00c-4287-86f9-80101793970b)

즉, 2번 FLAG는 22:45:48

이어서 music-player에서 MyPlayingList.dat에서 음악 파일을 확인 할 수 있었다.

![image](https://github.com/user-attachments/assets/22cadb86-53e6-4c10-8dbf-40d8d5f1b00a)
![image](https://github.com/user-attachments/assets/90f3860c-eecb-486c-8a43-f46963a242f7)

즉, 3번 FLAG는 Over the Horizon.mp3

계속해서 거주지를 알려면 날씨를 보면 될거 같아 DB로 날씨 db 파일을 열어보니 아래그림처럼 하남시가 나왔다.

![image](https://github.com/user-attachments/assets/c42d58c6-0d8a-417d-b892-4c191905135e)

4번 FLAG는 대한민국 하남시

그리고 magazine에 들어가 뉴스를 발행한 author도 찾을 수 있었다.
![image](https://github.com/user-attachments/assets/5532cf4a-7ae5-4af7-963c-d14b7f3b00c0)

5번 FLAG는 세계일보

마지막 1번 FLAG를 찾기 위해서 파일을 여러개 막 누르다 보니 manager-service 에서 applist를 찾을 수 있었다.

![image](https://github.com/user-attachments/assets/5e3e15f2-0aaf-4a2c-9131-bfef6c447ec2)

applist에 포함된 앱을 다 세어보니 21개 있었다. 

1번 FLAG는 21

5개 FLAG를 모두 조합하면 3S{21_22:45:48_Over the Horizon.mp3_대한민국 하남시_세계일보} 이다!!!
