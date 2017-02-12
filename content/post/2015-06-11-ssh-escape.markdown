---
categories: cli
comments: true
date: 2015-06-11T17:31:02Z
title: SSH Escape
url: /2015/06/11/ssh-escape/
---

ssh접속중 탈출하고 싶다면 탈출키 `~`를 사용한다.

<!--more-->

``` bash
% ssh acc@localhost
% ~? # 실제로 ~는 탈출키이므로 화면에 보이지 않는다.
Supported escape sequences:
 ~.   - terminate connection (and any multiplexed sessions)
 ~B   - send a BREAK to the remote system
 ~C   - open a command line
 ~R   - request rekey
 ~V/v - decrease/increase verbosity (LogLevel)
 ~^Z  - suspend ssh
 ~#   - list forwarded connections
 ~&   - background ssh (when waiting for connections to terminate)
 ~?   - this message
 ~~   - send the escape character by typing it twice
(Note that escapes are only recognized immediately after newline.)
```

ssh접속중 로컬작업이 필요하면 `~^Z`를 사용하면 된다.

백그라운드 잡 리스트를 보고 싶다면 `jobs` or `bg`  
다시 돌아가려면 `fg`(마지막 잡으로 복귀)  
특정잡으로 돌아가려면 `fg %숫자`  
종료하려면 `kill %숫자`[^ref]  




[^ref]: [Bg, Fg, &, Ctrl-Z – 5 Examples to Manage Unix Background Jobs](http://www.thegeekstuff.com/2010/05/unix-background-job/)

