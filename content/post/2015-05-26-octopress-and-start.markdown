---
categories: octopress
comments: true
date: 2015-05-26T20:39:26Z
description: octopress 정보 요약
keywords: octopress, blog, yaml, how to
title: Octopress &amp; Start
url: /2015/05/26/octopress-and-start/
---

## Intro
세상에는 매우 다양한 CMS[^cms]가 있다.  
인터넷이 국내에 막 대중에게 보급되기 시작한 1999년부터 지금(2015년)까지 블로깅을 하기위해 다양한 시도를 해봤다. 정적페이지에 직접 C로 작성한 CGI를 이용해 방명록과 게시판을 달아보기도 했고[^cgi] PHP로 사용자인증을 적용한 게시판을 만들어 사용해보기도 했다.  당시엔 직접만들어 쓰거나 아니면 누군가 만들어 놓은 CMS를 이용하기도 했는데 그땐 그런 툴이 몇 안되었기 때문에 선택이 쉬웠다. 사용하는 언어도 C, PHP, JAVA, JSP정도였고 DB도 Oracle아니면 MySQL정도였다.  운동도 보는것보다는 직접하는걸 더 좋아하는데 이런 성향은 개발에서도 나타난다. 누군가 만든걸 가져다 쓰는것보다는 직접 만들어 쓰는걸 더 선호한다.[^preference]
<!--more-->
어쨋거나... 컨텐츠보다는 프레임에 더 집착하다보니 유행과 관심사에 따라 뒤엎기를 반복하여 제대로 운영되는 블로그 하나 제대로 없다.

[Octopress](http://octopress.org/)는 웹서핑중 우연히 알게됐다.
Ruby로 만들어졌고 DB를 사용하지 않으며 Markdown을 사용하는 정적 블로깅 툴이다.
이런 특징때문에 [호스팅을 굉장히 쉽게 할 수 있다.](http://octopress.org/docs/deploying/)

사실 이 글은 이제막 설치하여 처음 작성해보는 글로 앞으로 어떤 험난한 여정이 남아있는지 모른다. 하지만 잠간동안 써본 느낌은...

  * 설치가 간단하다.
  * 포스팅이 쉽다.
  * 글쓰기에 집중하기 좋다.
  * 느리고 답답한 웹에디터를 사용하지 않아도 된다.
  * Git으로 관리되어 버전관리가 가능하며 안전하다.
  * 백업 및 이전이 쉽다.
  * [Markdown](http://en.wikipedia.org/wiki/Markdown), Ruby, [Sass](http://en.wikipedia.org/wiki/Sass_\(stylesheet_language\)), Git과 친해질 수 있다.

이제 남은건 커스터마이즈!  
즐겁게 사용해보자~

## Tip
  * [Blogging With Octopress: Add Comments](http://asaf.github.io/blog/2013/07/08/blogging-with-octopress-add-comments/)
  * zsh을 사용한다면 `alias rake='noglob rake'`를 사용해야 `zsh: no matches found` 에러가 발생하지 않는다.[^tip1]
  * AWS에서 [preview](http://octopress.org/docs/blogging/) 사용을 위해 포트열기.
    1. Security Groups에서 Inbound에 4000포트를 추가.
    1. `netstat -nap | grep LISTEN`에서 Local Address가 `127.0.0.1:4000`으로 나온다면 `Rakefile`에서 rackup에 호스트 옵션을 추가한다. "rackup `--host 0.0.0.0` --port #{server_port}"[^tip2]
  * [Heroku 도메인 연결](https://devcenter.heroku.com/articles/custom-domains#remove-a-domain)[^heroku_domain]
    * 목록보기 : `heroku domains`
    * 전체목록 : ``for app in `heroku apps`; do heroku domains --app $app; done``
    * 추가 : `heroku domains:add domain`
    * 삭제 : `heroku domains:remove domain`


## YAML Front Matter
포스트에 [YAML](https://ko.wikipedia.org/wiki/YAML)형식의 머리말 블록이 사용되며 다음과 같이 사용된다.
``` yaml
---
layout: post
title : Blogging Like a Hacker

# One category
categories: Sass
 
# Multiple categories example 1
categories: [CSS3, Sass, Media Queries]
 
# Multiple categories example 2
categories:
- CSS3
- Sass
- Media Queries
---
```

> 이 대쉬문자 사이에 사전-정의 변수 (설명은 아래를 참고하세요) 를 사용할 수 있고, 심지어는 자신만의 고유 변수를 정의할 수도 있습니다. 이 변수들은 해당 파일은 물론 해당 페이지 (또는 포스트) 에 연결된 레이아웃이나 include 파일에서 Liquid 태그를 사용하여 접근할 수 있습니다. at [jekyll](http://jekyllrb-ko.github.io/docs/frontmatter/)

이와 같이 사전정의 변수에 따라서 화면의 레이아웃이나 스타일을 쉽게 변경할 수 있는데 정의된 변수들과 기능이 무엇인지에 대한 가이드가 미비한거 같다.(내가 못찾는 건가...) 현재 사용중인 Yaml front matter를 정리해봤다.

| Name | Value | Desc |
| ---- | ----- | ---- |
| layout | post/page | 화면 레이아웃을 지정한다. 해당 레이아웃은 `source/_layouts`폴더에 있어야 한다. |
| title | String | 문서제목으로 사용된다. |
| date | Date(2015-05-26 20:39:26 +0900) | 문서 작성일 |
| comments | true/false | 커멘트 출력여부 |
| categories | String | 카테고리를 지정하며 다수일경우 스페이스로 구분한다 |
| tags | String | 카테고리와 유사하게, 하나 이상의 태그를 포스트에 추가할 수 있다. 또한, YAML 리스트 또는 공백문자로 구분하여 여러 태그를 지정할 수 있다. |
| permalink | | 생성된 블로그 포스트의 기본 URL을 사용하지 않고 다른 URL을 입력하면, 이것이 최종 URL로 사용된다. |
| published | true/false | `false`로 설정하면 `rake generate`나 `rake deploy`시 배포되지 않는다. 로컬에서만 `rake preview` 보인다. |
| sidebar | collapse | 사이드바를 숨긴다. |
| footer | true/flase | footer 출력여부 |
| sharing | true/false | 소셜 공유 버튼 출력 여부 |
| description | String | SEO 문서 정보 요약 |
| keywords | String | SEO Keyword, 구분은 쉼표 ex) yaml, tip, how to |
| external-url | url  | [Linklog](http://octopress.org/docs/blogging/linklog/)에 사용한다.[^1] |



## Useful Plugins & Sites
  * [Responsive video embed](https://github.com/optikfluffel/octopress-responsive-video-embed)
  * [octopus](http://chrissimpkins.github.io/octopus/) - the octopress commander
  * [Linklogs with Jekyll](http://mikebuss.com/2015/01/27/linklogs-with-jekyll/)
  * [Fixing the External-url (Linklog) Feature in Octopress](http://www.davidruttka.com/blog/2014/04/08/fixing-the-external-url-linklog-feature-in-octopress/)



## References
  * [Jekyll 영문문서](http://jekyllrb.com/)
  * [Jekyll 한글문서](http://svperstarz.github.io/jekyll-docs-ko/)



[^cms]: [List of content management systems](http://en.wikipedia.org/wiki/List_of_content_management_systems)
[^cgi]: 당시에는 인터넷 전용선이 아닌 전화라인으로 통신을 했기에 속도가 느렸다. 이런 이유로 C로 작성한 [CGI(Common Gateway Interface)](http://en.wikipedia.org/wiki/Common_Gateway_Interface)가 하나의 대안이였다.
[^preference]: 모든걸 다 만들어 쓰는건 아니지만 최소한 쓰더라도 어떻게 구현됐는지에 대한 호기심이 많다.
[^tip1]: [Blogging Basics](http://octopress.org/docs/blogging/)
[^tip2]: 이 문제로 `nmap localhost`, `netstat -nap`, `ufw`을 사용하면서 방화벽설정을 몇시간했는데 결국 Local Address문제였다. [127.0.0.0과 0.0.0.0과의 차이](http://stackoverflow.com/a/20778887), [The default host for Rails servers has changed in 4.2.](http://stackoverflow.com/a/27678343)
[^heroku_domain]: 도메인을 연결하려는데 `domain is currently in use by another app.`라는 메세지와 함께 연결이 되지 않았다. 도메인 설정을 해보고 가이드를 아무리봐도 되지 않았는데 이유는 예전에 사용하던 [postach.io](http://postach.io) 서비스에 도메인이 연결되었던듯 하다.(postach.io도 heroku사용중인듯...) 유료화되면서 더이상 제어를 할 수 없어 도메인 회수 방법을 고민중이다... 15.06.16 - [Shawn Adrian](http://nerdburn.com/)의 도움으로 해결했다. 역시 헤로쿠를 사용했고 내 도메인을 삭제했다는 답장을 받은후 등록이 잘 됐다.

[^1]: Blog와 Linklog의 좋은 예. [Tom Preston-Werner](http://tom.preston-werner.com/), [Daring fireball's Linked List](http://daringfireball.net/linked/)

