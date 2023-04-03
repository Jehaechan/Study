#### 소켓 프로그래밍 - <a href="https://velog.io/@emplam27/CS-%EA%B7%B8%EB%A6%BC%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%EC%86%8C%EC%BC%93-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D%EA%B3%BC-Handshaking" target="_blank">링크 참고</a>

#### TCP
![image](https://user-images.githubusercontent.com/71350045/229456837-5b6ba774-2823-49ee-8261-ec785325c796.png)

#### UDP
![image](https://user-images.githubusercontent.com/71350045/229545908-febd1de1-daab-44a2-9b00-1403807c9e65.png)

#### bind()란
- socket에 address/port를 연결 시킨다는 의미 - 외부에서 패킷이 들어왔을 때  이 패킷을 어디로 보내야 할지 정하는 것
- 특정 소켓과 커널을 연결시켜주는 것. 즉, 특정 address와 port로 데이터가 도착하면 어떤 프로세스로 보내라고 정해주는 것. 따라서 서버에서 bind()를 해야 한다.(sendto를 먼저 호출하는 서버 제외)

#### listen()이란
- 클라이언트의 연결 요청을 기다리는 소켓. 항상 연결 요청을 듣는 역할만을 수행한다. listen 소켓은 연결 요청이 오게 되면 본인이 복제한 새로운 소켓을 만들어 통신을 연결.(fork)

#### port/socket
- 한 port에 여러 socket descriptor를 열어둘 수도 있다. 이렇게 해야 여러 요청이 와도 큐에 쌓아두고 처리할 수 있는 것.

#### 프로세스로 패킷이 전달되는 과정
1. 랜카드를 통해 패킷 수신
2. 랜카드 드라이버가 운영체제에게 패킷 전달
3. 운영체제는 소켓 리스트에서 패킷의 목적지 address와 port 번호와 일치하게 bind되어있는 소켓을 찾음
4. 찾았을 경우에는 해당 어플리케이션에게 전달
5. 어플리케이션은 recvfrom을 통해 읽음

</br>

**bind하는 경우 -> 운영체제가 패킷을 어떻게 전달해야할지 모르는 경우(대부분 recvfrom을 먼저 호출하는 서버)**

</br>

**bind하지 않아도 되는 경우 -> sendto를 먼저 호출하는 경우.(sendto 호출 시 인자로 주어지는 상대방 주소 정보를 참조하여 운영체제가 자동으로 bind를 해주기 때문**

#### UDP socket
송수신 함수가 호출되는 순간에만 커널과 소켓이 연결됨. 즉, 매번 함수 호출마다 커널과 소켓을 연결했다 끊는 과정을 반복. 이 과정이 큰 overhead이므로 connect함수를 사용하여 address/port를 소켓에 미리할당해놓을 수 있다.(커널과 소켓과의 연결을 유지)
</br> 커널과 소켓 연결은 "소켓에 address/port 등록 -> 데이터 전송 -> 소켓에 등록된 address/port 초기화" 과정의 반복이다.
**connected UDP를 통해 특정 소켓에 미리 목적지 정보를 등록하고 send(), read()함수로 데이터 주고받을 수도 있다. 이렇게 되면 overhead가 감소하는 효과를 얻을 수 있다**

