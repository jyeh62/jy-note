---
type: docs/linux shell/ssh.md
keywords: hello world
created : 2021/06/02/18:16
tags: ssh
---

# ssh

원격서버에서 명령어를 실행하는 방법

- 한 줄의 명령어 실행 : ssh $user@$host command, user@$host sudo command
- 여러 줄의 명령어 실행 :

  - ssh user@server "command1; command2; script1"
  - ssh -T $HOST << EOL

    ```bash
        ssh -T admin@freebsdnas << EOL
        uptime
        date
        df -H
        echo "$var"
        echo "$HOSTNAME"
        EOL
    ```

  - ssh -T box1<<'EOL'

    ``` bash
        ssh -T vivek@server1<<'ENDSSH'
        var=$(date)
        echo "$var"
        ENDSSH
    ```

  - 로컬에 있는 스크립트 실행 : ssh user@hostname < file

    ``` bash
        cat cmd.txt
        date
        git clone url
        cd project
        make
        ssh vivek@ls.www-db-1 < cmds.txt
    ```

### 참고

<https://www.cyberciti.biz/faq/unix-linux-execute-command-using-ssh/>