---
categories: cli
comments: true
date: 2015-06-12T11:35:51Z
title: History Command
url: /2015/06/12/history-command/
---

Command line에서 명령을 실행하면 해당 명령이 기록에 남는다. 이 기록을 살펴보는 명령이 `history`다.


## 설정
이 설정은 bash기준이며 다른 쉘에서는 약간 다를 수 있다.[^1]
``` bash
export HISTTIMEFORMAT='%F %T '              # 로그에 날짜와 시간 출력
export HISTIGNORE="pwd:ls:ls -ltr:"         # pwd, ls, ls -ltr명령은 로그에 기록하지 않는다.
export HISTCONTROL=ignorespace              # 명령 실행시 처음 스페이스가 포함되면 로그에 기록하지 않는다.
export HISTCONTROL=ignoredups               # 명령이 중복되면 무시 (pwd를 연속으로 여러번 입력하면 로그에는 pwd하나만 기록된다)
export HISTCONTROL=erasedups                # 중복로그를 삭제하고 최근로그만 남긴다.
export HISTSIZE=450                         # 저장할 로그 개수 지정 (0을 지정하면 history 기능이 disable된다)
export HISTFILESIZE=450                     # 저장할 로그 개수 지정
export HISTFILE=/root/.commandline_warrior  # 히스토리 로그 파일 변경
```

<!--more-->

## 사용
  * `Ctrl+R`을 사용하여 history명령을 활용할 수 있다. Enter를 치면 검색한 명령이 수행된다. Backspace로 검색어를 삭제한뒤 재검색할 수 있다. 수정이 필요하면 esc나 화살키를 사용한다.
``` bash
# Ctrl+R
(reverse-i-search)`': 
```
  * 키보드의 `Up키`나 `Down키` 또는 `Ctrl+P`나 `Ctrl+N`으로 이전명령 다음명령을 선택하여 재실행 할 수 있다.
  * `!!` or `!-1` 바로 이전 명령을 재실행 한다.
  * `![no]`로 history number에 해당하는 명령을 재실행 한다.
  * `![keyword]`로 가장 최근의 키워드로 검색하여 재실행한다.
  * `vi !!:$` 이전명령의 마지막 인자 활용
  * `vi !!:[no]` 이전명령의 [no]인자 활용
  * `vi !^` 이전명령의 첫번째 인자 활용
  * `ls -l !cp:2` 최근 사용한 cp명령의 두번째 인자 활용
  * `history 53` 53부터 마지막까지 로그 출력
  * `history -10` 마지막부터 10개까지 로그 출력

`history -c`를 하면 히스토리기록을 모두 삭제한다.






## References
  * [15 Examples To Master Linux Command Line History](http://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history/)
  * [Using command history with the Korn shell](http://unixhelp.ed.ac.uk/shell/ksh_hist.html)


[^1]: [ksh은 안된다.](http://www.linuxquestions.org/questions/aix-43/history-cammand-with-date-format-705416/) 꼼수로 다음과 같이 사용한다. `PS1='$(printf "%(# %D %T )T" | read -s)$ '`
