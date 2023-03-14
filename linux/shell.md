https://hwan-shell.tistory.com/324

리눅스 쉘 -> 리눅스 커널과 통신하기 위한 도구. (default: /bin/bash)

쉘을 사용하기 위한 도구 -> 터미널(터미널은 하나의 프로세스)
다양한 명령어들은 이 터미널이 가지고 있는 정보를 기준으로 실행됨
명령어 실행시 터미널 정보를 기준으로 백그라운드에 자식프로세스가 fork되어 명령어를 실행
ex) mkdir "abc"
mkdir 프로세스를 fork. 해당 프로세스의 stdin으로 "abc"들어옴. 명령어 실행

PIPE사용시
ls | sort | less

![image](https://user-images.githubusercontent.com/71350045/224916601-b09fe456-948d-42bb-8de6-52f4540083fd.png)

이처럼 stdout / stdin 흐름으로 흘러간다.
ls sort less 총 3개의 프로세스가 생성되고, 이는 모두 터미널 프로세스가 exec하여 생기게 된다.
이들은 전부 병렬로 처리되고, 앞 프로세스의 stdout을 받기 전까지 뒤 프로세스는 wait상태가 된다.
각각 프로세스가 생성되는 것이므로 따로따로 sudo를 붙여줘야 한다.(만약 sudo 권한이 필요한 command일 경우)

xargs - build and execute command lines from standard input. 즉, 표준 입력으로부터 읽어서 명령어를 만든 다음, 해당 명령어를 수행하게끔 한다.
grep을 예로 들어 설명하자면, 원래 grep은 입력으로 전달된 파일의 내용에서 특정 문자열을 찾고자 할 때 사용하는 명령어이다.
ls -a | grep hi 를 하게 되면 stdin으로 ls -a결과를 받아 이 내용에서 특정 문자열을 찾게 된다.
그러나 echo file_name.txt | xargs grep hello를 하게 되면 echo file_name.txt의 결과를 표준입력으로 읽어 명령어를 만든다. 예를들어 file_name.txt 내용이 f1.txt f2.txt였다면
grep hello f1.txt f2.txt 다음과 같은 명령어를 만들어 실행하게 되는 것이다.
