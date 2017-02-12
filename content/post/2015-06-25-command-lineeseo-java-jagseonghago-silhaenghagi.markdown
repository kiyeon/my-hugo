---
categories: programming java
comments: true
date: 2015-06-25T14:27:21Z
title: Command line에서 java 작성하고 실행하기
url: /2015/06/25/command-lineeseo-java-jagseonghago-silhaenghagi/
---

이상하게도 java는 이클립스가 없이는 사용하기가 힘들다. 이클립스에 너무 익숙해져서 그럴지도 모르겠다. 간단하게 command line상에서 java를 작성하고 컴파일하고 실행해보자.

<!--more-->
## Writing code
Main class인 HelloWorld.java와 Library class인 Common.java를 만들어 compile과 package, import의 연관관계도 살펴보자.
``` java HelloWorld.java
package net.kiyeon;

import net.kiyeon.lib.Common;

public class HelloWorld
{
  public static void main(String args[])
  {
    Common.println("Hello World!");
  }
}
```

``` java Common.java
package net.kiyeon.lib;

public class Common
{
  public static void println(String str)
  {
    System.out.println(str);
  }
}
```


## Compile
Compile은 `javac`를 사용하며 라이브러리를 먼저 컴파일한다. 이때 `-d .`옵션을 줘야 package대로 폴더가 생성된다.
``` bash
% javac -d . Common.java
% tree
.
├── Common.java
├── HelloWorld.java
└── net
    └── kiyeon
        └── lib
            └── Common.class
```

다음으로 main class를 compile한다.
``` bash
% javac -d . HelloWorld.java
% tree
.
├── Common.java
├── HelloWorld.java
└── net
    └── kiyeon
        ├── HelloWorld.class
        └── lib
            └── Common.class
```

## Run
실행해보자.
``` bash
% java net.kiyeon.HelloWorld
Hello World!
```

## Jar
> [JAR]\(Java Archive, 자바 아카이브\)는 소프트웨어에서 수많은 자바 클래스 파일과 연관 메타데이터, 리소스(텍스트, 그림 등)을 하나의 파일로 모아서 자바 플랫폼에 응용 소프트웨어나 라이브러리를 배포하기 위한 패키지 파일 포맷이다. - [Wikipedia](https://ko.wikipedia.org/wiki/JAR_\(%ED%8C%8C%EC%9D%BC_%ED%8F%AC%EB%A7%B7\))

[jar]를 만들기 위해서는 `manifest.mf`가 필요한데 특별히 지정할 값이 없다면 jar가 디폴트로 생성한다.
``` bash
% jar cvf myprogram.jar net
added manifest
adding: net/(in = 0) (out= 0)(stored 0%)
adding: net/kiyeon/(in = 0) (out= 0)(stored 0%)
adding: net/kiyeon/lib/(in = 0) (out= 0)(stored 0%)
adding: net/kiyeon/lib/Common.class(in = 382) (out= 272)(deflated 28%)
adding: net/kiyeon/HelloWorld.class(in = 374) (out= 262)(deflated 29%)

% ls
Common.java  HelloWorld.java  myprogram.jar  net
```

myprogram.jar가 만들어졌다. 실행할때 `-cp` 옵션을 사용해야한다.
``` bash
% java -cp myprogram.jar net.kiyeon.HelloWorld
Hello World!
```

간편하게 실행시키려면 manifest.mf에 main-class를 지정하고 jar로 묶은뒤 java로 실행할때 `-jar`옵션을 주면 된다.
``` bash
% echo main-class: net.kiyeon.HelloWorld > manifest.mf

% jar cvmf manifest.mf myprogram.jar net
added manifest
adding: myprogram.jar(in = 1455) (out= 919)(deflated 36%)
adding: net/(in = 0) (out= 0)(stored 0%)
adding: net/kiyeon/(in = 0) (out= 0)(stored 0%)
adding: net/kiyeon/lib/(in = 0) (out= 0)(stored 0%)
adding: net/kiyeon/lib/Common.class(in = 382) (out= 272)(deflated 28%)
adding: net/kiyeon/HelloWorld.class(in = 374) (out= 262)(deflated 29%)

% java -jar myprogram.jar
Hello World!
```


## Jar에서 외부 Jar 참조
결론부터 말하자면 Jar안에 외부 Jar를 묶어 참조할 수 없다.[^1]

> Note: The Class-Path header points to classes or JAR files on the local network, not JAR files within the JAR file or classes accessible over Internet protocols. To load classes in JAR files within a JAR file into the class path, you must write custom code to load those classes. For example, if MyJar.jar contains another JAR file called MyUtils.jar, you cannot use the Class-Path header in MyJar.jar's manifest to load classes in MyUtils.jar into the class path. - [java documentation](https://docs.oracle.com/javase/tutorial/deployment/jar/downman.html)

외부 Jar를 함께 묶고 싶다면 압축을 푼후 Jar하나로 묶어 참조하거나 MANIFEST.MF에 `Class-Path`를 지정하거나 (MANIFEST.MF에서 Class-Path는 Main-Class가 있는 폴더의 상대경로로 지정한다.)
``` java MANIFEST.MF
Manifest-Version: 1.0
Created-By: 1.7.0_06 (Oracle Corporation)
Main-Class: net.kiyeon.HelloWorld
Class-Path: lib/ojdbc6.jar
```

java실행시 `-cp`옵션으로 classpath를 함께 지정하는 것이다. classpath지정시 윈도우는 `;`를 구분자로 사용하고 리눅스계열은 `:`를 구분자로 사용한다.
``` bash
% java -cp ./lib/ojdbc6.jar:myprogram.jar net.kiyeon.HelloWorld
```

## References
  * [Working with Manifest Files: The Basics](https://docs.oracle.com/javase/tutorial/deployment/jar/manifestindex.html)
  * [Difference between JAR, WAR and EAR files](https://kkpaidi.wordpress.com/2013/07/15/difference-between-jar-war-and-ear-files/)
  * [JAR, WAR, and EAR - what are these files used for?](http://geekexplains.blogspot.kr/2008/06/jar-war-and-ear-what-are-these-files.html)

## CLI Programming library
  * [JCommander](http://jcommander.org/) - Because life is too short to parse command line parameters
  * [Commons CLI](http://commons.apache.org/proper/commons-cli/) - The Apache Commons CLI library provides an API for parsing command line options passed to programs. It's also able to print help messages detailing the options available for a command line tool.
  * [Commons IO](http://commons.apache.org/proper/commons-io/) - Commons IO is a library of utilities to assist with developing IO functionality.
[jar]: https://docs.oracle.com/javase/tutorial/deployment/jar/build.html


[^1]: [외부 라이브러리를 포함한 JAR파일 만들기](http://veenvalu.tistory.com/14), [Jar 생성시 dependency 라이브러리 처리(포함)하기](http://blog.cjred.net/164)
