---
categories: programming
comments: true
date: 2015-06-23T09:06:39Z
published: false
tags: tool
title: Regular Expression
url: /2015/06/23/regular-expression/
---

>정규 표현식(正規表現式, 영어: regular expression, 간단히 regexp 또는 regex)은 특정한 규칙을 가진 문자열의 집합을 표현하는 데 사용하는 형식 언어이다. 정규 표현식은 많은 텍스트 편집기와 프로그래밍 언어에서 문자열의 검색과 치환을 위해 지원하고 있으며, 특히 펄과 Tcl은 언어 자체에 강력한 정규 표현식을 구현하고 있다.
<!--more-->
>
>컴퓨터 과학의 정규 언어로부터 유래하였으나 구현체에 따라서 정규 언어보다 더 넓은 언어를 표현할 수 있는 경우도 있으며, 심지어 정규 표현식 자체의 문법도 여러 가지 존재하고 있다. 이 중 표준화된 것으로는 POSIX의 확장 정규 표현식이 있으며, 표준화되지는 않았지만 펄의 정규 표현식과 그 대체 구현인 PCRE도 널리 사용된다.
>
>문자열을 토큰으로 변환하는 과정에서 텍스트를 좌에서 우로 검사하면서 여러 규칙과 일치될 문자의 수량을 다양하게 시도하는 처리에 가장 적합할 뿐 파싱에는 적절치 않다. - Wikipedia

[정규표현식]으로 HTML 파싱하려고 시도했는데 위의 설명과 같이 파싱에는 적절치 않았다.[^1]

역참조

## References
  * [Regular Expressions and Other Pattern Matching](http://billposer.org/Linguistics/Computation/Resources.html#patterns)

## Language References
  * [Java Tutorials: Regular Expressions](http://docs.oracle.com/javase/tutorial/essential/regex/index.html)
  * [Perl Regular Expressions documentation](http://perldoc.perl.org/perlre.html)
  * [Python Regular Expressions Tutorial](https://pythonspot.com/regular-expressions/)
  * [Ruby 2.2.2 Regular Expressions Tutorial](http://ruby-doc.org/core-2.2.2/doc/regexp_rdoc.html)

## Learn Regular Expression
  * [RegexOne](http://regexone.com/) - Learn regular expressions with simple, interactive examples.
  * [Regular-Expressions.info](http://www.regular-expressions.info/)

## Library & Tools
  * [Regex101](https://regex101.com/) - Online regex tester and debugger
  * [RegExLib](http://www.regexlib.com/) - Regular Expression Library
  * [Regular Expressions Cheat Sheet](http://www.cheatography.com/davechild/cheat-sheets/regular-expressions/)
  * [Search for interactive regular expression testers](https://www.google.co.kr/search?q=online+regex+tester&gws_rd=cr&ei=2bGIVZqVMYOf8QWIw4LQAQ)

[^1]: [Using regular expressions to parse HTML: why not?](http://stackoverflow.com/a/590789), [Parsing Html The Cthulhu Way](http://blog.codinghorror.com/parsing-html-the-cthulhu-way/)

[정규표현식]: https://en.wikipedia.org/wiki/Regular_expression
