# Shell Script 예제

## Application 관련
1. APP 로그파일에서 ERROR 로그 저장
```shell script
if [ $# neq 2 ];
then
  echo "사용법 : app-log.sh [MONTH] [DAY]"
  exit 100
fi

NODE=`hostname`
DATE=`date +%Y%m%d%H%M`
LOGDIR=/home/application/logs
STATUS=`cat /var/log/messages |egrep "error|ERROR|Error" | wc -l`
LOGFILE=$LOGDIR/$NODE.testlogstatus.$STATUS.$DATE
if [ "$STATUS" -gt 0 ];
then
	grep error /var/log/messages > $LOGFILE
fi
```

## Nginx 관련
## OS 관련
1. system log 에서 error message 파일에 출력
```shell script
NODE=`hostname`
DATE=`date +%Y%m%d%H%M`
LOGDIR=/home/test/logs
STATUS=`cat /var/log/messages |egrep "error|ERROR|Error" | wc -l`
LOGFILE=$LOGDIR/$NODE.testlogstatus.$STATUS.$DATE
if [ "$STATUS" -gt 0 ];
then
	grep error /var/log/messages > $LOGFILE
fi
```
## 프로세스 관련
## 디렉토리 관련
1. 사용량 10% 이상의 디렉토리 출력
```shell script
#!/bin/bash

mount_used=(`df -h`)
cnt=0

for mount_used_item in ${mount_used[*]}
do
        if [[ "$mount_used_item" =~ [1-9][0-9][%] ]]; then
                cnt3=$cnt
                cnt2=`expr $cnt + 1`
                echo "사용률:${mount_used[$cnt3]}, 디렉토리: ${mount_used[$cnt2]}"
        fi
        cnt=`expr $cnt + 1`
done

