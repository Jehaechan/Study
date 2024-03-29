<a href="https://www.google.co.kr/](https://plummmm.tistory.com/211" target="_blank">참고자료</a>

### 프로세스 생성
- fork()이용: 부모는 하나 뿐, 자식은 여러개 가능

### fork()
- Code영억을 제외한 메모리 영역을 복사한다. (Code영역은 공유)
- 그러나 복사는 비효율적 -> Copy-On-Write(COW) 이용
- **COW**: 모든 영역을 공유하고 있다가 둘 중 한 쪽에서 수정하고자 할 때만 page 단위로 복사

### fork() 이후 file descriptor 관리
- fork()하게 되면 동일한 파일 디스크립터를 공유
- 동알한 offset을 공유하고 있으므로 꼬일 확률이 높다
- 따라서 다음과 같은 해결법 사용
1. 부모가 파일에 작업할 내용이 없다면, 자식이 작업을 완료할 때 까지 기다렸다 갱신
2. 둘 다 작업을 해야하는 경우, fork 이후 사용하지 않는 fd를 닫는다(주로 network server)

### vfork() - 부모 프로세스와 자식 프로세스가 모든 것을 공유(복사 X)
- fork()이후 exec()로 다른 프로그램을 실행할 때 사용
- 불필요한 copy를 막는 효과가 있다
- **vfork()이후 자식이 먼저 실행되도록 보장(fork 함수의 경우 자식이 먼저 시작될지 부모가 먼저 시작될지 모름)**

### 프로세스 종료
1. main() function의 return에 의한 종료 -> exit()함수 호출과 동일한 결과
2. exit()함수 호출 -> 할당된 메모리, open file 등을 정리한 뒤 종료
3. _exit(), _Exit() 함수 호출 -> 아무 작업을 하지 않고 커널로 바로 반환
4. abort()호출 - SIGABRT 발생. 현재 상태를 core dump 뜬 다음 비정상 종료
5. 특정 시그널에 의한 종료 - 자기 자신 / 다른 프로세스 / 커널이 보낼 수 있음.(ex. seg. fault는 커널에 의해서 발생하는 시그널)

**(4, 5는 비정상 종료)**</br>
**_exit()은 System Call 함수. 나머지는 라이브러리 함수.**

### 프로세스 상태
1. **좀비 프로세스**: 자식프로세스가 종료되었으나 부모가 아직 종료상태를 회수하지 않은 경우. 정확히 얘기하자면 부모는 wait call을 통해 자식의 종료를 기다리고 있지만, 자식은 이미 종료되어 신호를 보낼 수 없는 상황.
2. **고아 프로세스**: 부모가 자식보다 먼저 종료되는 경우. 이 경우 PID 1번 즉, init 프로세스가 대신 부모가 된다.

일반적으로 프로세스가 종료되면, 커널은 모든 활성 프로세스를 탐색하여 고아 프로세스가 있는지 확인. 또한, init 프로세스는 자식 프로세스 중 하나가 종료되면 wait()류 함수를 호출하여 종료상태를 회수하므로 좀비 프로세스가 되지 않는다.

### 프로세스 종료 상태 회수
wait류 함수 - 부모가 자식이 종료될 때 까지 기다리게 하고, 종료 상태를 회수할 때 쓰는 함수.
- 자식이 종료되면 커널이 부모에게 SIGCHLD 시그널을 보낸다. 보통은 이 시그널을 무시하지만, 자식 종료를 기다리려면 wait()함수를 등록해 해당 시그널을 처리하도록 하는 것.

### 리눅스 프로세스 구조
1. 터미널을 켠다 -> init 프로세스가 fork().
2. exec()로 fork된 프로세스를 getty라는 프로그램으로 변경(getty: 터미널 관리 프로그램) **현재 상황: init - getty**
3. getty가 exec()로 login 프로그램 실행 **현재 상황: init - login**
4. 현재 작업 디렉토리를 사용자의 홈 디렉토리로 변경(chdir)
5. root에서 사용자로 권한을 변경(chown)
6. 터미널 장치에 대한 접근 권한을 설정
7. setgid, initgroups를 호출하여 사용자의 group id를 설정
8. login이 가진 정보로 환경을 초기화
9. exec()을 호출하여 shell로 프로그램을 변경 - execl("/bin/sh", "-bash", 0); **현재 상황: init - bash**

![image](https://user-images.githubusercontent.com/71350045/229716945-6eb8c353-92ef-4b58-8165-e54de109ec8e.png)

### 용어 정리
- **PID:** 프로세스 식별을 위해 부여
- **PPID:** 부모 프로세스의 PID
- **PGID:** 프로세스 그룹 식별을 위해 부여
- **SID:** 세션 식별을 위해 부여

### 프로세스 그룹: 동일한 터미널로부터 신호들을 받을 수 있는 하나 이상의 프로세스들의 집합
- 프로세스 그룹은 프로세스들을 수평적으로 묶는 것(Tree 구조 X) 프로세스들은 부모-자식 관계를 통해 수직적으로 묶이게 된다.
- pgid == pid 이면 해당 프로세스가 그 그룹의 리더가 된다.
- setpgid(pid_t pid, pid_t pgid) 함수를 통해 특정 프로세스의 PGID를 지정할 수 있다. setpgid(0,0)다음과 같이 호출하면 본인이 새로운 그룹을 생성해서 리더가 되는 것.

### 프로세스 세션: 프로세스 그룹들의 집합
- setsid()를 통해 새로운 세션을 생성 가능
- 프로세스 그룹 리더는 setsid()를 호출할 수 없음
- 만약 프로세스 그룹의 리더가 아닌 프로세스가 setsid를 호출한다면?
1. 새로운 세션 생성
2. 호출한 프로세스는 새로운 세션의 세션 리더가 됨
3. 호출한 프로세스는 새 프로세스 그룹의 리더가 됨
4. 호출한 프로세스는 제어 터미널이 없음
- 한 세션에 하나의 포그라운드 프로세스 그룹만 존재할 수 있다. 백그라운드 프로세스 그룹은 여러개 존재할 수 있다. (포그라운드 프로세스는 하나 이하일듯? 데몬 세션은 포그라운드 프로세스가 없을 것이니)

**4.의 의미란 setsid()를 통해 생성한 세션은 Daemon이 된다는 뜻인것 같다. 결국 위 그림에서 결국 SID 0에 속한 애들을 제외한 나머지 session들은 daemon으로 돌아가고 있는 것이다. Daemon프로세스들은 사용자와 소통할 필요가 없기 때문이다** 

**특정 프로세스가 A Session에서 B Session으로 옮겨갈 수는 없는 듯.</br>
부모의 Session을 따라가고, 만약 새로운 session을 만들고 싶다면 그룹 리더가 아닌 프로세스가 새 Session을 만들고 그 Session의 리더가 되는 것.</br>
프로세스 그룹의 리더가 새로운 Session을 만들면서 다 같이 데려가는 것도 불가능**

### 제어 터미널
![image](https://user-images.githubusercontent.com/71350045/229727887-60b0e86c-bed4-48fb-a806-92a6a068bf2e.png)

세션에는 보통 연관된 제어 터미널이 있다. 제어 터미널은 보통 사용자가 로그인한 터미널 장치이다.</br>
제어 터미널과 연결을 성립한 세션의 리더를 **제어 프로세스(controlling process)** 라고 한다.</br>
- 터미널이 끊어지면 제어 프로세스가 SIGHUP 시그널을 받게 된다
- 어느 시점에서든 세션 내 프로세스 그룹 중 하나는 포그라운드 프로세스 그릅으로서 터머닐에서 입력을 받고 터미널로 출력을 보낼 수 있다
- 사용자가 제어 터미널에서 인터럽트 문자를 입력하면, 터미널 드라이버는 포그라운드 프로세스 그룹을 중지시키는 시그널을 보낸다

## 결론
결국 프로세스 관리를 위해 세션 -> 그룹 -> 프로세스 순으로 묶임. 모든 프로세스들에게 전달되어야 하는 시그널 (ex. 터미널 종료)은 세션 리더한테 전달해서 모든 프로세스로 뿌려버리고, 특정 그룹에만 전달되어야 하는 시그널은 그룹 리더에게 갈 듯</br>
여러 프로세스 그룹을 두고 전부 background group인데 그중 하나는 foreground로 사용자와 입출력을 주고받게 만들어주는 느낌인듯.</br>
세션은 결국 데몬 프로세스들을 만들기 위해 도입된 개념인 듯

### 데몬(Daemon)
**Daemon process != background process**
- 일반적인 background process는 터미널을 가지지만, Daemon은 터미널을 가지지 않음
- 모든 데몬 프로그램은 PPID가 1번으로 setting된다.
- 데몬 프로그램을 만드는 법
1. fork 호출
2. 부모프로세스 종료 -> PPID 1번이 됨
3. setsid로 새로운 세션 생성 -> 세션 리더가 된다
4. chdir를 이용해 프로세스가 루트 디렉토리에서 작업을 수행하도록 함 (데몬 프로세스가 작업할때 상대경로를 사용함으로써 생기는 혼동을 피하기 위해)

### 데몬의 stdout, stderr, stdin을 끊어버리는 법
```C
close(1)
close(2)
close(3)
fd0 = open("/dev/null", O_RDWR);
fd0 = open("/dev/null", O_RDWR);
fd0 = open("/dev/null", O_RDWR);
```
close(0), close(1), close(2)로 stdout, stderr, stdin을 전부 닫는다.</br>
그 다음 /dev/null을 open해주면 open함수는 비어있는 file descriptor중 가장 작은 번호부터 리턴해주기에 0, 1, 2번이 /dev/null과 연결됨을 알 수 있다.


https://leeyh0216.github.io/posts/sid_pid_ppid_pgid/
이후에 정리

syslogd - daemon 프로세스들의 로깅을 도와주는 데몬 프로세스. UDP 포트 514번 / named pipe / kernel을 통해 등의 방법으로 syslogd로 데이터를 보내면 syslogd 데몬이 로깅을 도와주는 구조인듯. 후에 
