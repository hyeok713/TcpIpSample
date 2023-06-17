2023.04.09

이 코드는 TCP/IP 통신을 하는 것을 배워 두고 싶어서 찾아본 코드를 이용 해서

작성한 TCP/IP 이해 예제 코드 입니다.

그냥 TCP/IP 통신에 대해서 아무것도 모르는 상태에서 그것들은 어떻게 동작을 하는 가에 대해서

이해를 얻기 위해서 작성 되었습니다. 

이 코드를 작성해 보면서 알게된 것은 

TCP/IP 통신을 위해서 socket을 열어 서버에 접속이 되었다면 
종료 없이 서버를 다시 열게 되면 앞에서 연결 되었던 서버는 무용지물이 되어 
서버와 통신을 할 수 없게 된다는 것 입니다. 

서버와 통신을 시작 했다면, 종료를 반드시 시켜 주어야 합니다.  다시 할 때는 서버와 다시 접속을 시작 해야 하고...

응답을 수신하는 부분은 무한 수신 대기 모드를 유지 하여 수신 정보가 없을 때 까지 유지 하도록 하여 데이터를 받으면 됩니다.

전송하는 부분은 필요시 마다 데이터를 전송 하여 처리를 진행 하면 됩니다.

서버쪽 테스트는 python 코드를 이용해서 했습니다. 해당 코드는 아래 와 같이 echo 서버를 구성하여 데이터 전송하면 
자동 으로 전송된 값을 응답하는 구조로 구성을 했습니다.

아직 초보적인 코드이기 때문에 그냥 참고용으로만 보세요. 

-------------------------------------------------------------------------------------------
import socket
from random import random

addr = ("0.0.0.0", 3000)  # 포트번호 4444
with socket.socket() as s:  # 소켓 할당

    s.bind(addr)  # 소켓 바인딩
    s.listen()  # client의 연결요청 대기
    print("Server is started... 3000 ")

    # ------------------------------------서버 개통과정--------------

    conn, addr = s.accept()  # client 연경 요청을 수락
    print("accept {}:{}".format(addr[0], addr[1]))  # 연결된 client 정보 출력
    while (1):  # 무한반복
        data = conn.recv(1024)  # client가 보낸 메시지를 data에 저장
        if data.decode() == "finished":  # data decoding 결과가 finished면
            break  # 반복문 탈출
        respData = data.decode() + "..." + str(random())
        conn.send(respData.encode())  # data를 그대로 client에게 전송
        print(data.decode())  # 보낸 데이터 읽기
    print("Server finished")  # 서버 종료 알리기

print("SOCKET closed... END")