<!DOCTYPE html>
<html>
<head>
        <link rel="stylesheet" type="text/css" href="sytle.css">
</head>
<body>
        <h1>[2025 SWING Forensic Study] sleepingshark Write-up</h1>
</body>
<br>
<br>
</html>

문제 파일 ```dump.pcap```를 Wireshark로 열어보았다.

우선 http를 살펴보자.

![image](https://github.com/user-attachments/assets/0bc43339-d348-413f-9729-672daf63e303)

계속 POST 요청을 하고 있는데 뒤에 PATH 부분이 눈에 띈다.

![image](https://github.com/user-attachments/assets/f8153bd7-7fc4-46f4-821a-44a8160bed4b)

아무래도 SQL 쿼리문으로 flag를 찾는것 같다. 일단 가장 맨위에 있는 쿼리를 URL decoder로 돌려보았다.

```q=SELECT%20IF%28ASCII%28SUBSTRING%28%28SELECT%20flag%20FROM%20s3cr3t%20LIMIT%201%29%2C35%2C1%29%29%3D156%2C%20SLEEP%283%29%2C%200%29```

![image](https://github.com/user-attachments/assets/d026599f-e570-46b3-ac82-be3d72120925)

```q=SELECT IF(ASCII(SUBSTRING((SELECT flag FROM s3cr3t LIMIT 1),35,1))=156, SLEEP(3), 0)```

디코딩한 구문을 살펴보면

1. ```(SELECT flag FROM s3cr3t LIMIT 1)``` : s3cr3t 테이블의 flag 열에서 첫 번째 레코드를 가져온다.

2. ```SUBSTRING(..., 35, 1)``` : 가져온 flag 문자열에서 35번째 문자 하나를 추출한다.

3. ```ASCII(...)``` : 해당 문자 하나의 ASCII 값을 추출한다.

4. ```IF(...=156, SLEEP(3), 0)``` : 그 ASCII 값이 156이라면 3초간 대기(SLEEP(3))하고 그렇지 않으면 아무 일도 일어나지 않는다.

모든 쿼리문이 이런 구조이니 문자열에서 n번째 문자열을 가져와 특정 ASCII 값과 같으면 3초간 대기한다는 것을 알 수 있다.

그렇다면 http 패킷을 다 확인해 봐야 한다는 건데... 

![image](https://github.com/user-attachments/assets/177e455f-1610-4873-93d3-86ae283a96f6)

총 39개를 다 볼 자신이 없어서 구글링해보았다.

찾아보니 pcap 파일을 분석해주는 파이썬 라이브러리 dpkt가 있다고 한다.

```python
import dpkt
import datetime
from dpkt.compat import compat_ord
import time
import urllib.parse

def analyze_packets(pcap):
    """Print out information about each packet in a pcap

       Args:
           pcap: dpkt pcap reader object (dpkt.pcap.Reader)
    """
    flag = list()
    for i in range(39):
        flag.append('X')
    post_sent = False
    payload = ''
    request_time = 0
    response_time = 0
    # For each packet in the pcap process the contents
    for timestamp, buf in pcap:


        eth = dpkt.ethernet.Ethernet(buf)
        # Make sure the Ethernet data contains an IP packet
        if not isinstance(eth.data, dpkt.ip.IP):
            print('Non IP Packet type not supported %s\n' % eth.data.__class__.__name__)
            continue
        ip = eth.data
        tcp = ip.data
        if not post_sent and tcp.dport == 80 and len(tcp.data) > 0:
            # Print out the timestamp in UTC
            ts_utc = datetime.datetime.utcfromtimestamp(timestamp)
            print('Timestamp: ', str(ts_utc), '(' + str(timestamp) + ')')
            request_time = timestamp
            
            http = dpkt.http.Request(tcp.data)
            payload = urllib.parse.unquote(http.uri)
            print('-- request --\n {0} {1}\n'.format(http.method, payload))
            if(http.method == 'POST'):
                post_sent = True
        elif post_sent and tcp.sport == 80 and len(tcp.data) > 0:
            # Print out the timestamp in UTC
            ts_utc = datetime.datetime.utcfromtimestamp(timestamp)
            print('Timestamp: ', str(ts_utc), '(' + str(timestamp) + ')')
            response_time = timestamp

            http = dpkt.http.Response(tcp.data)
            print('-- response --\n{0}'.format(http.status))

            if(response_time - request_time >= 2.8):
                payload = payload[payload.find('LIMIT 1),') + 9:]
                idx = int(payload[:payload.find(',')]) - 1
                ch = chr(int(payload[payload.find('))=') + 3:payload.find(', SLEEP(3)')]))
                flag[idx] = ch
                print('\n\nFound!!\n\n flag[{0}] : {1}\n\ncurrent flag : {2}'.format(idx, ch, ''.join(flag)))
                time.sleep(0.1)
            post_sent = False
    return ''.join(flag)


def test():
    """Open up a test pcap file and print out the packets"""
    with open('/content/dump.pcap', 'rb') as f:
        pcap = dpkt.pcap.Reader(f)
        print('flag : ' + analyze_packets(pcap))



if __name__ == '__main__':
    test()
```

코드를 실행하면 

![image](https://github.com/user-attachments/assets/dfef66c1-b38b-4621-b88c-9be6df97ba65)

flag가 나온다.

flag : GoN{T1mE_B4s3d_5QL_Inj3c7i0n_wI7h_Pc4p}

**<전체코드>**
https://colab.research.google.com/drive/1giNLCX9XdTZy7txqJkDZOQ_fefCHTASX?usp=sharing


**<참고자료>**
https://dpkt.readthedocs.io/en/latest/print_http_requests.html


