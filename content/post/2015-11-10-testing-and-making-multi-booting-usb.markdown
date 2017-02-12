---
categories: etc
date: 2015-11-10T23:56:57Z
title: Making & Testing Multiboot USB
url: /2015/11/10/멀티-부팅-USB-제작과-테스트/
---

## 개요
요즘은 그렇지 않지만 예전에는 뭔가 OS를 설치할일이 많았다. 특히 Windows는 정기적으로 설치해야하는 일이 빈번했다. 

Windows외에 테스트용 리눅스 서버나 복원용 툴, 고스트, 네트워크 모니터링을 위한 백트랙등 가끔씩 설치할일이 있는데 아무래도 USB로 설치하는게 간편하다. 원래는 부트영역에 한번에 하나만 설치가 가능하고 그래서 OS별로 USB를 준비하던가 그때마다 USB를 포맷하고 새로 설치하는 번거로움이 있다.  이번에 [MLC] 대용량 USB를 구입하면서 남는 용량이 아깝기도 하고 간혹가다 설치를 해줘야할 때도 있고해서 [멀티부트](https://en.wikipedia.org/wiki/Multiboot_Specification) USB를 만들기로 했다.





## 준비물
* USB
* [YUMI]
* OS [Image](https://en.wikipedia.org/wiki/ISO_image) File
* [가상머신 Tools](https://en.wikipedia.org/wiki/Virtual_machine)([VMware](http://www.vmware.com/kr),  [Parallels](http://www.parallels.com/kr/), [Virtual Box](https://www.virtualbox.org/) ...)
* [Plop Boot Manager](https://www.plop.at/en/bootmanager/download.html)


## 멀티부트 USB 만들기
멀티부트 디스크를 만드는 방법은 두가지가 있는데 첫번째는 직접 부트영역을 뜯어고치는 방법과(MBR, GRUB), 멀티부트를 만들어주는 유틸리티를 사용하는 것이다. 당연히 두번째 방법으로 만들것이고, YUMI[^1]를 사용할 것이다.

### 1. Format
USB 부팅은 BIOS에서 지원을 해줘야 하며 BIOS에서 읽을 수 있는 File System으로 포맷을 해야한다. FAT32나 NTFS 두가지를 지원하므로 둘중 하나를 선택해서 USB를 포맷한다.[^2]

두 파일 시스템에 장단점이 있는데... FAT32은 거의 모든 시스템에서 지원하기 때문에 호환성이 좋고 그래서 USB의 성능이 잘 나온다. 하지만 파일당 최고 4GB용량 제한이 있다. NTFS는 파일당 최고 16EB로 사실상 제한이 없긴 하나 호환성이 극히 나쁘다. Windows를 제외한 대부분의 시스템에서 이 파일시스템을 지원하지 않는다.[^3]

타 시스템에서 NTFS를 사용하려면 [ntfs-3g](http://www.tuxera.com/community/open-source-ntfs-3g/)나 [Paragon NTFS](https://www.paragon-software.com/home/ntfs-mac/)를 설치하면 사용할 수 있다.

ntfs-3g는 무료지만 업데이트가 중단되었는지 제역할을 하지 못한다. 읽기,쓰기 속도를 측정([Disk Speed Test](https://itunes.apple.com/kr/app/blackmagic-disk-speed-test/id425264550?mt=12))해보면 `Write 3 MB/s, Read 20 MB/s`가 나온다.

Paragon NTFS는 유료지만 제대로 속도가 나온다. `Write 120 MB/s, Read 200 MB/s`

모두 OS X에서 측정했으며 몇가지 더 테스트 해봤다.

* FAT32 : `Write 114 MB/s, Read 202.5 MB/s`
* [JFS] : `Write 130 MB/s, Read 200 MB/s`, OS X에서 사용하는 파일 시스템으로 윈도우에서는 인식하지 못한다.
* ExFAT : `Write 100 MB/s, Read 180 MB/s`

참고로 백업용으로 USB를 사용하려면 4k 쓰기 속도가 좋아야 하는데 내껀 4k쓰기 속도가 거의 0이였다. 조금 좌절했지만 특이한건 JFS나 Ext4를 사용하면 4k 쓰기 속도와 상관없이 엄청난 성능을 발휘한다. NTFS에서는 프리징이 발생한다.(아 MS ...)

### 2. [YUMI]
[YUMI]는 인터페이스가 직관적이여서 사용하기가 쉽다. USB를 선택하고 설치할 OS를 선택하고 Image를 지정하면 바로 설치가 되고 이어서 설치를 하면 멀티부트가 자동으로 만들어진다.[^4]





## Test
멀티부트 USB가 잘만들어졌는지 테스트 해보자.

### PC에서 테스트
1. PC에 USB를 꼽는다.(USB 3.0포트에서 인식이 안될 수도 있으니 2.0에서도 해본다)
1. 파워를 켜고 BIOS나 부트선택메뉴에서 USB를 선택한다.(모델별로 진입키가 다르다. 처음 부팅시 화면에 나오므로 잘보고 키를 누른다)
1. 멀티부트 메뉴가 나온다면 성공.

### 가상머신에서 테스트
나는 VMWare를 사용하는데 예전에는 일반PC처럼 부팅시 진입키로 USB를 선택할 수 있었는데 그 메뉴가 사라져서 다른 방법을 사용해야 한다.

[Plop Boot Manager](https://www.plop.at/en/bootmanager/download.html)를 다운받아 압축을 해제하면 `plpbt.iso`가 있다. VMWare CD-ROM에서 이녀석을 선택하고 부트오더에서 CD-ROM을 최상단에 둔다.[^6]

전원을 켜면 `Plop Boot Manager`가 실행되고 여기서 `USB`를 선택하면 USB로 부팅이 된다. USB3.0 포트는 인식을 못한다.(VMware가 못하는건지 Plop이 못하는건지는 모르겠다...) 2.0포트를 사용해야 한다.





## References

* [Test of six linux filesystems ext2, ext3, jfs, reiserfs, vfat, xfs on linux 2.4.5](http://web.archive.org/web/20040407211142/aurora.zemris.fer.hr/filesystems/)
* [What File System Should I Use for My USB Drive?](http://www.howtogeek.com/73178/what-file-system-should-i-use-for-my-usb-drive/)
* [NTFS, FAT와 exFAT에 대 한 기본 클러스터 크기](https://support.microsoft.com/ko-kr/kb/140365)





## 결론
멀티부트 USB 만드는거 참 쉬운데 내가 사용하는 환경이 OS X다 보니 알아볼게 많았다. 파일 시스템 호환과 장단점 USB 메모리 타입과 성능및 속도, 가상머신 테스트 방법... 참 별거 아닌거 같으면서도 가장 기초적인 부분까지 파고 들 수 있어서 재미있었던거 같다. 이 과정에서 128GB USB를 날렸다. ㅠㅠ 28만개 파일을 백업했는데 바로 인식 불가...

참고로

* FAT32와 NTFS의 클러스터 크기에 따라 속도와 효율이 달라진다. 클러스터 크기가 낮으면 저장공간을 아낄 수 있지만 속도가 느려진다. 클러스터 크기가 높으면 속도는 빠른대신 저장공간 낭비가 많아진다.
* 우분투를 처음 설치했을때 root 패스워드는 없는 상태이다. `sudo passwd root`를 하여 root 패스워드를 지정할 수 있다. 또는 그냥 `sudo <command>`를 하면 루트권한으로 커맨드가 실행된다.[^5]





[yumi]: http://www.pendrivelinux.com/yumi-multiboot-usb-creator/
[mlc]: https://en.wikipedia.org/wiki/Multi-level_cell
[jfs]: https://en.wikipedia.org/wiki/JFS_(file_system)
[brew]: http://brew.sh/
[^1]: YUMI말고도 다양한 툴들([List of tools to create Live USB systems](https://en.wikipedia.org/wiki/List_of_tools_to_create_Live_USB_systems))이 있다.
[^2]: 정확하지는 않다. 경험으로 알게된건데 찾기 귀찮아서... --;
[^3]: [Comparison of file systems](https://en.wikipedia.org/wiki/Comparison_of_file_systems)
[^4]: [USB 메모리 하나로 여러 운영체제 설치 - YUMI](http://massukr.tistory.com/155)
[^5]: [What is the default root password when was never set at installation time?](http://askubuntu.com/a/434956)
[^6]: [VMware USB 부팅 하기... 첫번째 방법...](http://prolite.tistory.com/165), [VMware USB 부팅 하기... 두번째 방법...](http://prolite.tistory.com/166)



