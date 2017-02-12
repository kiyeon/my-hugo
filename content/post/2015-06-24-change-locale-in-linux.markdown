---
categories: linux
comments: true
date: 2015-06-24T09:28:08Z
tags: cli howto
title: Change locale in linux
url: /2015/06/24/change-locale-in-linux/
---

Locale은 **'지역, 언어'**정보로 프로그램은 입/출력시 사용자가 설정한 locale에 따라 인코딩을 적용하여 메세지를 출력한다.[^1]

## 현재값
현재 설정값을 보려면 **[locale]**을 사용한다.

```bash
% locale
LANG=en_US.utf8                # LC_* 값들을 설정하지 않았을때 적용되는 기본 값
LANGUAGE=
LC_CTYPE="en_US.utf8"          # Character classification and case conversion.
LC_NUMERIC="en_US.utf8"        # Non-monetary numeric formats.
LC_TIME="en_US.utf8"           # Date and time formats.
LC_COLLATE="en_US.utf8"        # Collation order(알파벳 정렬과 관련된 규칙).
LC_MONETARY="en_US.utf8"       # Monetary formats.
LC_MESSAGES="en_US.utf8"       # Formats of informative and diagnostic messages and interactive responses.
LC_PAPER="en_US.utf8"          # Paper size.
LC_NAME="en_US.utf8"           # Name formats(이름 표기 형식. first name, last name).
LC_ADDRESS="en_US.utf8"        # Address formats and location information.
LC_TELEPHONE="en_US.utf8"      # Telephone number formats.
LC_MEASUREMENT="en_US.utf8"    # Measurement units (Metric or Other).
LC_IDENTIFICATION="en_US.utf8" # Metadata about the locale information.
LC_ALL=                        # LC_*의 값들이 override 된다.
```

<!--more-->
Locale은 다음과 같은 포맷으로 정의된다.
`<lang>_<territory>.<codeset>[@<modifiers>]` = `en_US.utf8`


## 변경 가능한 목록
변경 가능한 값을 보려면 **[locale] -a** or **[localedef] --list-archive**를 사용한다.
``` bash
% locale -a
C
C.UTF-8
en_US.utf8
POSIX

% localedef --list-archive
en_US.utf8
```

locale데이터는 `/usr/lib/locale/locale-archive` 파일에 기록되는데 위 커맨드는 이 아카이브파일에 저장된 목록을 보여준다.
``` bash
% ls -lh /usr/lib/locale/locale-archive
-rw-r--r-- 1 root root 1.3M Jun 24 10:22 /usr/lib/locale/locale-archive
```


## 지원하는 목록
시스템에서 지원하는 목록은 `/usr/share/i18n/SUPPORTED`에 기록되어있다.
``` bash
% cat /usr/share/i18n/SUPPORTED | wc -l
457


% cat /usr/share/i18n/SUPPORTED
# This file names the currently supported and somewhat tested locales.
# If you have any additions please file a glibc bug report.
aa_DJ.UTF-8 UTF-8
aa_DJ ISO-8859-1
aa_ER UTF-8
aa_ER@saaho UTF-8
aa_ET UTF-8
af_ZA.UTF-8 UTF-8
af_ZA ISO-8859-1
am_ET UTF-8
...
```

## Locale 추가
지원하는 Locale을 추가할 수 있으며 추가하게 되면 `/usr/lib/locale/locale-archive`에 기록이 된다. 추가할때는 **[locale-gen]**을 사용한다. 지원하는 Locale 목록을 보고 루트권한으로 추가한다. 여기서는 한국어와 프랑스어를 추가한다.
``` bash
% cat /usr/share/i18n/SUPPORTED | grep -Ei '(ko_kr|fr_fr)'
fr_FR.UTF-8 UTF-8
fr_FR ISO-8859-1
fr_FR@euro ISO-8859-15
ko_KR.EUC-KR EUC-KR
ko_KR.UTF-8 UTF-8


% sudo locale-gen ko_KR.utf8 fr_FR.utf8
Generating locales...
  fr_FR.UTF-8... done
  ko_KR.UTF-8... done
Generation complete.


% ls -lh /usr/lib/locale/locale-archive
-rw-r--r-- 1 root root 2,8M juin  24 11:26 locale-archive # 용량이 늘어났다.


% localedef --list-archive # 2개의 locale이 추가되었다.
en_US.utf8
fr_FR.utf8
ko_KR.utf8
```

## 사용
추가된 Locale을 사용하려면 환경변수 `LANG`에 해당 Locale을 지정하면 된다.
``` bash
% export LANG=fr_FR.utf8


% locale
LANG=fr_FR.utf8
LANGUAGE=
LC_CTYPE="fr_FR.utf8"
LC_NUMERIC="fr_FR.utf8"
LC_TIME="fr_FR.utf8"
LC_COLLATE="fr_FR.utf8"
LC_MONETARY="fr_FR.utf8"
LC_MESSAGES="fr_FR.utf8"
LC_PAPER="fr_FR.utf8"
LC_NAME="fr_FR.utf8"
LC_ADDRESS="fr_FR.utf8"
LC_TELEPHONE="fr_FR.utf8"
LC_MEASUREMENT="fr_FR.utf8"
LC_IDENTIFICATION="fr_FR.utf8"
LC_ALL=


% date
mercredi 24 juin 2015, 21:35:59 (UTC+0900)


% export LANG=ko_KR.utf8
% date
2015. 06. 24. (수) 21:40:54 KST
```


## 삭제
추가된 Locale을 삭제할때는 `locale-gen --purge`를 사용하며 이때 인자로 주어진 Locale을 제외한 모든 Locale이 삭제된다.
``` bash
% sudo locale-gen --purge en_US.utf8 ko_KR.utf8
Generating locales...
  en_US.UTF-8... done
  ko_KR.UTF-8... done
Generation complete.

% localedef --list-archive
en_US.utf8
ko_KR.utf8
```


## 시스템 기본값 설정
locale을 시스템 기본값으로 변경하려면 `/etc/default/locale`에서 `LANG`의 값을 변경한후 리부팅을 하면 된다.
``` bash
% cat /etc/default/locale
# Created by cloud-init v. 0.7.5 on Wed, 10 Dec 2014 23:55:44 +0000
LANG="en_US.UTF-8"
```


## 정리
  * [locale], [localedef] - locale 조회
  * [locale-gen] - locale 추가, 제거(--purge)
  * `/usr/share/i18n/SUPPORTED` - locale 지원 목록
  * `/usr/lib/locale/locale-archive` - 추가된 실제 locale archive
  * `/etc/default/locale` - 시스템 기본 locale
  * `export LANG=ko_KR.UTF-8` - locale 적용



## References
  * [HowTo : Change Locale (Language and Character Set) in Linux](http://www.shellhacks.com/en/HowTo-Change-Locale-Language-and-Character-Set-in-Linux)
  * [How can I uninstall a locale via command-line?](http://askubuntu.com/a/58200)


[locale]: http://man.he.net/?topic=locale&section=all
[localedef]: http://man.he.net/?topic=localedef&section=all
[locale-gen]: http://man.he.net/?topic=locale-gen&section=all

[^1]: [Locale at Wikipedia](https://ko.wikipedia.org/wiki/%EB%A1%9C%EC%BC%80%EC%9D%BChttps://ko.wikipedia.org/wiki/%EB%A1%9C%EC%BC%80%EC%9D%BC)
