---
categories: linux
comments: true
date: 2015-06-09T10:27:57Z
published: true
tags: cli
title: 프로그래머를 위한 ack(like-grep)
url: /2015/06/09/peurogeuraemeoreul-wihan-ack-like-grep/
---

[grep]은 텍스트에서 키워드를 찾아내는 툴이다. 다음과 같이 사용한다.
``` bash
% lsb_release --all | grep -i desc
Description:    Ubuntu 14.04.2 LTS
```

데이터가 많으면 해당 키워드를 찾기가 힘든데 이때 컬러옵션[^grep_color]을 사용할 수 있다.
``` bash
% cat /etc/services | grep --color=auto -i http
# Updated from http://www.iana.org/assignments/port-numbers and other
# sources like http://www.freebsd.org/cgi/cvsweb.cgi/src/etc/services .
http            80/tcp          www             # WorldWideWeb HTTP
http            80/udp                          # HyperText Transfer Protocol
https           443/tcp                         # http protocol over TLS/SSL
https           443/udp
http-alt        8080/tcp        webcache        # WWW caching service
http-alt        8080/udp
hkp             11371/tcp                       # OpenPGP HTTP Keyserver
```

<!--more-->

grep은 컬러지정에 한계가 있고 키워드가있는 라인만 출력된다. 전체적으로 내용이 표시되며 특정 키워드마다 색을 다르게 적용하고 싶었는데 [ack]를 발견했다.

[ack]는 빠르고, 소스코드를 찾는 프로그래머를 위해 만들어졌다. 순수 펄로작성되어 펄(5.8.8 이상)이 설치된 어느 머신에서나 간편하게 사용할 수 있다.

[ack 설치](http://beyondgrep.com/install/)
`curl http://beyondgrep.com/ack-2.14-single-file > ~/bin/ack && chmod 0755 !#:3`

[ack]사이트에 가면 [ack를 사용한 다양한 툴들](http://beyondgrep.com/more-tools/)을 모아놨는데 [hhighlighter](https://github.com/paoloantinori/hhighlighter)를 사용하면 쉽게 파일내 키워드들에 highlight를 적용할 수 있다.
`cat file | h foo bar bat`

vim이나 emacs, TextMate를 위한 플러그인도 있으며 다른 grep-like 툴들도 안내하고 있다.

**Ack in five minutes**
[Rocky Mountain Ruby 2012 Lightning Talk: Ack by Cameron Pope](https://www.youtube.com/watch?v=sKmyl5D8Da8)


[^grep_color]: grep에서 찾은 키워드의 컬러를 변경할 수 있는데 GREP_COLOR환경변수를 사용한다. `GREP_COLOR="1;33;40"`. 값은 [안시컬러](http://www.pixelbeat.org/docs/terminal_colours/)를 사용한다.

[grep]: http://unixhelp.ed.ac.uk/CGI/man-cgi?grep
[ack]: http://beyondgrep.com/
