https://hwan-shell.tistory.com/324

리눅스 쉘 -> 리눅스 커널과 통신하기 위한 도구. (default: /bin/bash)

쉘을 사용하기 위한 도구 -> 터미널(터미널은 하나의 프로세스)
다양한 명령어들은 이 터미널이 가지고 있는 정보를 기준으로 실행됨
명령어 실행시 터미널 정보를 기준으로 백그라운드에 자식프로세스가 fork되어 명령어를 실행
ex) mkdir "abc"
mkdir 프로세스를 fork. 해당 프로세스의 stdin으로 "abc"들어옴. 명령어 실행

## PIPE사용시
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


## Redirection
키보드로 stdin을 받거나 터미널로 stdout을 하는 것이 아닌, file로 stdin을 받고 file로 stdout을 할 수 있는 기능.
- [command] < [file] : 파일의 내용을 명령어의 입력으로 사용
- [command] > [file] : 명령어의 결과로 나오는 stdout을 파일에 덮어쓴다.
- [command] >> [file] : 명령어의 결과를 파일에 추가한다.
</br>

- [command] 1 > [file] : 표준 출력을 파일에 덮어쓴다
- [command] 2 > [file] : 명령어 실행 과정에서 에러가 발생하면, 에러 내용을 파일에 덮어쓴다
- [command] 1 > [file1] 2 > [file2]: 명령어 실행 시, 결과는 file1에 에러는 file2에 덮어쓴다.
- [command] 1 > [file] 2 > [file]: 명령어 실행시 결과와 에러 모두 파일에 덮어쓴다.
</br>

- [command] 1 > [file] 2 > /dev/null : 명령어 실행시 결과는 파일에 쓰고, 에러는 출력하지 않고 버린다.
- [command] > [file] 2>&1 : stdout은 file에 쓰고, stderr를 &1로 보낸다. &1이란 표준출력(1)으로 보내는데 백그라운드(&)로 보내라는 의미. 백그라운드는 화면에 보이지 말고, 안보이게 처리하라는 의미. 결국 stdout과 stderr 모두 file에 쓰여지게 된다.
- [command] &> [file] : https://www.gnu.org/software/bash/manual/bash.html#Redirections 결국 command 결과를 background로 전부 file로 보내라는 의미이므로 stdout / stderr 전부 file에 쓰게되는 것 같다.
