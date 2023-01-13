### 1.1. lilo로부터 커널 이미지 vmlinuz 로딩까지
1. PC 부팅 -> BIOS에 저장되어 있는 초기화 프로그램 실행
1. 초기화 프로그램에 의해 메모리 체크등이 수행
1. 하드디스크 0번 섹터의 부트 프로그램을 읽음 - 보통 0번 섹터를 MBR이라 한다. 여기에는 lilo, grub 등 리눅스를 위한 부트로더가 있을 수도 있고, NT나 OS/2등 다른 운영체제의 부트로더가 들어 있을 수도 있다.
1. 

#### MBR(Master Boot Record)이란?
- HDD의 0번 섹터 즉, 첫 번째 데이터 저장 공간. 512byte의 영역에 하드디스크의 파티션 정보를 가지고 있다. 따라서 단일 파티션에서는 MBR이 존재하지 않고, 다중 파티션의 경우에만 존재한다.   ![image](https://user-images.githubusercontent.com/71350045/212245249-d5561095-b396-4037-b5ca-a8966722cb2e.png)<br/>파티션의 첫 번째 섹터에는 VBR(Volume Boot Record)가 위치. 이 VBR은 파티션에 설치된 OS를 부팅해주기 위해 부트로더를 호출하는 역할. MBR은 각 파티션별 VBR의 위치를 기록하고 있게 된다.
- **Volume**<br/>운영체제가 사용 가능한 섹터들의 집합. 즉, 물리적으로 연속된 공간이라는 개념이 아닌 섹터들의 집합
- **Sector**<br/>Track을 구성하는 요소. HDD에서 가작 작은 데이터 기록 단위이며, 512byte.

* 부트로더란?
HW는 BIOS에서 메모리 검사 등이 끝난 이후, HDD의 MBR을 읽어들이게 된다. MBR 하나당 한 개의 OS이미지를 저장할 수 있다.
<참고>
https://wiki.kldp.org/Translations/html/LAME/index.html
http://wiki.kldp.org/KoreanDoc/html/Boot_Process-KLDP/index.html
https://jackeros.tistory.com/26
https://lemonpoo22.tistory.com/23
