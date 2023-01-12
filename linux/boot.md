#### 1.1. lilo로부터 커널 이미지 vmlinuz 로딩까지
1. PC 부팅 -> BIOS에 저장되어 있는 초기화 프로그램 실행
1. 초기화 프로그램에 의해 메모리 체크등이 수행
1. 하드디스크 0번 섹터의 부트 프로그램을 읽음 - 보통 0번 섹터를 MBR이라 한다. 여기에는 lilo, grub 등 리눅스를 위한 부트로더가 있을 수도 있고, NT나 OS/2등 다른 운영체제의 부트로더가 들어 있을 수도 있다.
1. 

* MBR(Master Boot Record)이란?
- OS

* 부트로더란?
HW는 BIOS에서 메모리 검사 등이 끝난 이후, HDD의 MBR을 읽어들이게 된다. MBR 하나당 한 개의 OS이미지를 저장할 수 있다.
<참고>
https://wiki.kldp.org/Translations/html/LAME/index.html
http://wiki.kldp.org/KoreanDoc/html/Boot_Process-KLDP/index.html
https://jackeros.tistory.com/26
https://lemonpoo22.tistory.com/23
