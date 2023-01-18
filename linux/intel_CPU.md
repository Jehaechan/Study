### Cache
- 캐시는 라인(Line)단위로 관리
- 캐시라인은 일정한 크기로 나누어짐(일반적으로 32Byte, 64Byte, 128Byte 등)
- Cache line은 Cache coherenece에 매우 중요한 역할을 한다 -> 1Byte를 읽어도 Cache line 단위로 메모리부터 읽어오며, 데이터를 메모리에 쓸 때 역시 캐시라인 단위로 쓰여지게 됨<br/>(Memory의 데이터 전송 단위, HDD(SSD)의 데이터 전송 단위 등에 대해서도 후에 정리하기. 섹터가 HDD의 가장 작은 단위이고 이걸 어떤 단위로 메모리에 올리고 이런 내용들)


### 링버스 구조
- Nehalen Microarchitecture: 코어간 통신을 위해 각 코어가 개별적으로 L3캐시로 연결된 별도의 버스를 사용.(L3 캐시가 도입되기 이전엔 FSB를 통해 코어간 통신) <br/>![image](https://user-images.githubusercontent.com/71350045/213231746-93338e53-bfbd-4a6d-be62-eccf8d251941.png)
<br/>![image](https://user-images.githubusercontent.com/71350045/211787461-f91e498f-0ba2-49a1-854d-3297c90fd0b2.png)
- 위와 같이 L3 캐시를 이용할 경우 모든 코어와 L3캐시를 각각 독립된 채널을 통해 1대1로 연결해주어야 한다 -> 코어의 개수가 늘어날수록 설계의 복잡성이 커짐
- 해당 문제를 해결하기 위해 링버스 구조를 도입<br/>![image](https://user-images.githubusercontent.com/71350045/213232735-5199031d-91e1-47a2-99d7-27f88c4bbfaf.png)
- 링버스 구조는 접근하고자하는 캐시와 코어의 거리에 따라 latency가 달라지는 문제가 있음. 코어 개수가 늘어나면 특정 case에서 latency가 커지게 된다
- 위와 같은 문제로 인해 서버 등에 사용되는 고성능 CPU에서는 링버스가 아닌 Mesh Interconnect Architecture를 도입하였다

### QPI(Quick Path Interface)
- QPI는 cpu와 cpu 및 다른 부속품들과의 통신(PCIe 등)을 위해 만들어진 기술. 즉 소켓이 여러개인 서버용 PC등에 사용된다. 한 cpu내에서 통신은 L3 캐시 / FSB / 링버스 구조 등을 이용한다.<br/>![image](https://user-images.githubusercontent.com/71350045/213233321-0ea1ea65-55dd-40c6-84b3-f87bb262c8e4.png)
- 즉 그냥 QPI는 버스 프로토콜 중 하나인것 같다. 메인보드상에 연결된 bus로 데이터를 전송하는데, 여기에 사용되는 프로토콜이라고 이해하면 될것 같다.

### 참고
https://cookis.net/360
