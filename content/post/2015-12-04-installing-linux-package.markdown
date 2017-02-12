---
categories: linux
date: 2015-12-04T14:55:00Z
title: 리눅스 패키지 설치하기
url: /2015/12/04/리눅스-패키지-설치하기/
---

## Ubuntu

### 기본
Something along the lines of:
dpkg --> apt-get, aptitude --> Synaptic, Software Center
http://askubuntu.com/a/40781

### 최신 버전 설치
1. 최신 패키지 검색  
[Ubuntu package building and hosting](https://launchpad.net/ubuntu)에서 최신 패키지를 검색한다.
2. **Binary packages built by this source**에서 설치할 Binary package를 선택한다.
3. **Published versions**에서 시스템에 맞는 버전을 선택한다.  
ex) [sqlite3 3.9.2-1(amd64 binary) in ubuntu xenial](https://launchpad.net/ubuntu/xenial/amd64/sqlite3/3.9.2-1)
4. **Downloadable files**에서 deb파일을 다운로드 한다.  
`wget http://launchpadlibrarian.net/225502540/sqlite3_3.9.2-1_amd64.deb`
5. 다운로드한 deb을 설치한다.  
`sudo dpkg -i sqlite3_3.9.2-1_amd64.deb`
6. 의존성 관련 패키지가 필요하면 **Package relationships**에서 deb을 받아 설치한다.
7. 설치 완료한 패키지를 실행해본다.  
{{< highlight sh >}}
% sqlite3
SQLite version 3.9.2 2015-11-02 18:31:45
Enter ".help" for usage hints.
Connected to a transient in-memory database.
Use ".open FILENAME" to reopen on a persistent database.
sqlite>
{{< / highlight >}}

