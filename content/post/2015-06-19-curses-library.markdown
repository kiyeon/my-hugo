---
categories: cli library
comments: true
date: 2015-06-19T11:10:32Z
title: Curses Library
url: /2015/06/19/curses-library/
---

콘솔을 좋아하는 이유? 심플하기때문에... 가볍고 빠르고 어디서든 사용할 수 있다는점.

> [curses]는 유닉스 계열 운영체제를 위한 터미널 제어 라이브러리의 하나로, 텍스트 사용자 인터페이스(TUI) 응용 프로그램의 구성을 가능케 한다. 이 이름은 "커서 최적화"(cusor optimization)에서 따온 것이다. 문자 셀 터미널(VT100 따위) 상에서 응용 프로그램의 디스플레이를 관리하는 명령들이 모인 라이브러리이다. - wikipedia

![screenshot](https://upload.wikimedia.org/wikipedia/commons/1/11/Jack-curses-screen.gif)

<!--more-->

콘솔에 머무르는 시간이 많다보니 필요한 정보들을 한 화면에서 제어하고 싶었고 그럴때 필요한 curses라이브러리를 찾아봤다.

## Java Curses Library[^1]  [^2]
  * [Charva](http://www.pitman.co.za) - A Java Windowing Toolkit for Text Terminals.
    * [Charva API documentation](http://www.pitman.co.za/projects/charva/api/index.html)
  * [Java Curses Library](http://sourceforge.net/projects/javacurses/) - JCurses, a java console windowing toolkit for Windows and Linux
  * [Blacken](https://code.google.com/p/blacken/) - Blacken is a library which provides an enhanced console-like interface. 
  * [Lanterna](https://code.google.com/p/lanterna/) - Lanterna is a Java library allowing you to write easy semi-graphical user interfaces in a text-only environment, very similar to the C library curses but with more functionality.

## Shell Curses Library
  * [Shell Curses function library](http://www.ibm.com/developerworks/aix/library/au-shellcurses/#resources) - "Shell Curses" is a library of script functions that provide the shell programmer the ability to perform text-based cursor movements to specified locations on the screen.
  * [Bashsimplecurses](https://code.google.com/p/bashsimplecurses/) - Create simple ncurses windows with bash
  * [Dialog](http://hightek.org/projects/dialog/) - Dialog is a utility to create nice user interfaces to shell scripts, or other scripting languages, such as perl. It is non-graphical (it uses curses) so it can be run in the console or an xterm. There are also graphical Xwindows versions mentioned below.


[curses]: https://ko.wikipedia.org/wiki/Curses

[^1]: [Curses in Java](http://www.drdobbs.com/open-source/curses-in-java/228700553)
[^2]: [What's a good Java, curses-like, library for terminal applications?](http://stackoverflow.com/q/439799)
