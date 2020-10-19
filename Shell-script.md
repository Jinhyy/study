# ShellScript
## 쉘 스크립트
1. 개요
    - 사용자, 응용프로그램이 커널에게 명령을 내릴수 있도록 하는 프로그램

2. 작성 방법
    ```shell script
    #!/bin/bash       * 번역기로 /bin/bash 사용한다.
    echo -e 'hello world\n\n\n'
    ```
3. 줄끝 문자
    - 리눅스에서 줄끝 문자는 hex로는 '0A' 이다. ( '\n' )
    - but, 윈도우 혹은 다른 운영체제에서 줄끝문자는 리눅스와 틀리기 때문에 주의가 필요하다.
    - hexdump -C [파일명] 으로, 줄끝문자가 '0a'가 맞는지 확인할 수 있다.

### System Montiroing / DevOps






