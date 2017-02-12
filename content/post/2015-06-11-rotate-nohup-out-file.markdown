---
categories: cli
comments: true
date: 2015-06-11T09:07:45Z
title: rotate nohup out file
url: /2015/06/11/rotate-nohup-out-file/
---

nohup은 터미널이 종료되어도 프로세스를 종료시키지 않고 동작시킬때 사용한다.[^1]
``` bash
% nohup COMMAND        # nohup.out에 로그가 기록된다.
% nohup COMMAND > FILE # FILE에 로그가 기록된다.
```

표준출력과 표준에러는 출력파일을 지정하지 않으면 디폴트로 nohup.out에 기록된다.

로그파일을 지우면 프로세스는 출력파일을 잃게되고 프로세스를 재기동하지 않는 이상 로그를 볼 방법이 없다. 디버깅이 어려워지는 상황이 되는것이다. 물론 로그를 표준출력에 의지하지 않고 내부 로깅처리를 한다면야 상관없겠지만 간단한 스크립트에 그런 코드까지 추가한다는게 번거롭고 쉽지 않기도 하다.

<!--more-->

mknod로 fifo파일을 만들고 nohup의 출력을 fifo파일로 한다. cat에서 fifo를 입력으로 받아 출력방향을 정한다. 표준출력은 `>`, 표준에러는 `2>` 표준출력과 같은 파일에 표준에러를 쓰려면 `&1`을 준다.[^3] 주의할점은 프로세스실행후 처음 fifo에 대한 출력을 한번 뚫어(?) 줘야 한다는거다. 그렇지 않으면 프로세스가 stop상태로 동작을 하지 않는다. 아마 출력장치가 접속되길 기다리는거 같다. 한번 출력지정후엔 출력파일이 끊겨도 계속 동작한다.[^2]
``` bash
mknod /tmp/mypipe p
cat < /tmp/mypipe > /tmp/myreallog
nohup myapplication >/tmp/mypipe
```


To rotate the log
``` bash
mv /tmp/myreallog /tmp/rotatedlog
kill [pid of the cat process]
cat < /tmp/mypipe >/tmp/myreallog
```

## References
* [rotate nohup out file (nohup.log)](http://silviud.blogspot.kr/2011/01/rotate-nohup-out-file-nohuplog.html)

## Alternative
* [flog](http://linux.softpedia.com/get/Utilities/flog-12382.shtml) - flog (file logger) is a program that reads input from STDIN and writes to a file.
* [rotatelogs or logrotate](http://xp-rience.blogspot.kr/2010/07/using-roratelogs-to-rotate-nohupout.html)


[^1]: 터미널이 종료되면 사용자의 모든 프로세스에 hangup signal이 전달되고 이 시그널을 받은 프로세스들은 종료절차를 밟는다.
[^2]: Ubuntu에서는 출력파일이 없어지면 **broken pipe  nohup**이 발생하면서 프로세스가 종료된다.
[^3]: `COMMAND > stdout.log 2> &1`
