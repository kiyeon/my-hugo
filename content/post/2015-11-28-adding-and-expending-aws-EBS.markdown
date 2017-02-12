---
categories: aws
date: 2015-11-28T18:40:00Z
title: AWS EBS 확장및 추가하기
url: /2015/11/28/adding-and-expending-aws-EBS/
---

AWS EC2의 디스크가 Full이 되어 확장이 필요했다.


## 볼륨 확장

[Linux에서 EBS 볼륨의 스토리지 공간 확장](http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-expand-volume.html)

1. 인스턴스를 Stop 시킨다.
2. EBS Volume을 찾아 Snapshopt을 만든다. (복사용)
3. Snapshot에서 2에서 만든 스냅샷을 선택하여 새로운 볼륨을 만든다. 여기서 확장할 사이즈를 정한다. `ex) 8GB -> 15GB`, 볼륨이 커지면 비용이 발생하므로 [SIMPLE MONTHLY CALCULATOR](https://calculator.s3.amazonaws.com/index.html)로 요금을 계산하자.
4. 이전 볼륨을 분리(Detach Volume)하고 새로 만든 볼륨을 연결(Attach Volume)한다.
5. 인스턴스를 다시 시작한다.
6. 파일 시스템을 확장한다. 여기서부터는 [Linux 파일 시스템 확장](http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ebs-expand-volume.html#recognize-expanded-volume-linux)참조


## 볼륨 추가

[인스턴스에 볼륨 추가](http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/ec2-add-volume-to-instance.html)

1. 추가하려는 인스턴스의 가용 영역(Availability Zone)을 확인한다. 추가 볼륨의 가용 영역이 인스턴스와 같아야 연결할 수 있다.
2. EBS메뉴에서 Volumes를 선택하여 새로운 볼륨을 생성한다.(볼륨 유형, 크기, 가용 영역 입력)
3. 새로 생성된 볼륨을 연결(Attach Volume)한다. 생성한 볼륨의 상태가 `available`이 되면 인스턴스에 연결 할 수 있다.
4. 인스턴스에 접속하여 볼륨을 연결한다.
  * `lsblk` or `sudo fdisk -l | grep Disk`로 연결된 볼륨을 확인 할 수 있다. (`blkid`로 UUID와 File system을 확인 할 수 있다.)
  * 새 볼륨을 사용하려면 포맷을 해야한다. `sudo mkfs -t ext4 /dev/xvdf`로 포맷.
  * 포맷된 디바이스를 mount로 연결한다. `sudo mkdir /mnt/my-data`, `sudo mount /dev/xvdf /mnt/my-data`

### 볼륨 추가 후 fstab 설정

부팅 될때마다 mount를 유지하려면 `/etc/fstab`에 마운트 정보를 기록해야 한다.

`fstab`은 리눅스가 부팅될 때마다 자동으로 읽는 중요한 파일이므로 수정 시에 주의가 필요하다. 글자가 틀릴 경우 부팅 자체가 아예 안될 수 있다.

fstab에는 6개의 필드가 있다.
{{< highlight shell >}}
# 장치이름     마운트될 디렉토리  파일 시스템 속성       dump 사용 여부  파일 시스템 체크 여부
/dev/sdb1    /home/mydata   ext3     defaults   0             1
{{< / highlight >}}

1. device : 장치명이 들어간다. UUID나 Lable등이 사용가능하다. `blkid`로 확인.
2. mount point
3. fstype
4. options : defaults로 설정하면 읽기/쓰기/실행 등의 작업이 대부분 가능하다.
5. dump : 1로 설정하면 리눅스 dump명령으로 백업이 가능하다.
6. fsck : 부팅시 이 파티션을 체크하는데 1인 파일 시스템을 먼저 체크하고 2는 1을 체크한 후에 체크한다. 0은 체크를 생략하며 그로인해 부팅속도가 향상된다.




## Etc

* 현재 디스크의 파일 시스템을 확인하고 싶다면 `df -T` 또는 `blkid`를 입력한다.[^1]
* Ubuntu에 접속할때 `*** /dev/xvda1 should be checked for errors ***`가 나온다면... 디스크체크[^2]
  * `sudo vi /etc/default/rcS`
  * `#FSCKFIX=no`를 주석처리하고 `FSCKFIX=yes`를 추가 (automatically repair filesystems with inconsistencies during boot)
  * `sudo touch /forcefsck`
  * reboot
  * 리부팅후 `/forcefsck`는 없어진다.
  * `FSCKFIX=yes`를 제거한다.


## References
* [Amazon Web Services](https://aws.amazon.com/ko/)
* [Amazon Elastic Compute Cloud 설명서](Amazon Elastic Compute Cloud 설명서)



[^1]: [https://kldp.org/node/93119](https://kldp.org/node/93119)
[^2]: [http://serverfault.com/a/679051](http://serverfault.com/a/679051)
