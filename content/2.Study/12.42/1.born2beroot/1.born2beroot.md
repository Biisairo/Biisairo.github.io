# born2beroot

## Install

[install](https://www.notion.so/1def567adde1448d83225131eabca03b?pvs=21)

## Hard disk file type

### VDI - virtualbox disk image

- virtualbox 에서만 사용하는 포맷
- 다른 컴퓨터에서 해당 가상환경을 사용할때 virtualbox가 설치되어 있어야함

### VHD - virtual hard disk

- Oracle - virtualbox, ms - hyper-x, citrix - xen 에서 사용 가능한 포맷
- 다른 컴퓨터에서 사용하려면 위와 마찬가지로 셋중 하나의 가상화 소프트웨어가 설치되어 있어야함

### VMDK - virtual machine disk

- Oracle - virtualbox, VMware - VMware player 에서 사용 가능한 포맷
- 위와 마찬가지로 둘 중 하나가 설치되어 있어야함

## linux file system

확인 - `lsblk`

장치이름 - 첫번째 발견된 하드디스크 : /dev/sda - s : scsi 방식의 - small computer system interface : 컴퓨터와 주변기기의 연결을 위한 인터페이스 - d : disk 중에 - a : a 번째 - 두번째 발견된 하드디스크 : /dev/sdb - 첫번째 ~ 네번째 파티션 : sda1 ~ sda4 - 첫번째 논리 파티션 : sda5 - 첫번째 발견된 cd rom : /dev/sr0

- /
    - root
    - 모든 파일의 최상단
- bin
    - binary
    - 명령어들이 들어있는 디렉토리
- boot
    - 부팅에 필요한 이미지 파일과 부팅정보를 가진 디렉토리
- dev
    - 연결된 장치들의 정보가 저장
- etc
    - 시스템 환경설정이 들어가 있음
- home
    - 사용자 디렉토리들이 저장
- root
    - 위의 / 와는 다름
    - 시스템 관리자의 홈 디렉토리
    - 일반 사용자는 접근할 수 없음
- sbin
    - bin 과 비슷하나 root 유저만 사용할 수 있는 명령어들이 있음
- sys
    - 리눅스 커널 관련 정보가 들어있음
- tmp
    - 임시 데이터가 들어있는 디렉토리
    - 부팅시 초기화
- usr
    - 기본실행파일, 라이브러리, 헤더파일등이 저장됨
    - 대부분의 응용프로그램과 파일이 저장되어 있음
- var
    - 시스템 운영중 만들어진 로그가 저장
- srv
    - 주로 서버를 위해 만드는 디렉토리
    - 다른 시스템에게 제공되는 파일들이 저장됨
- proc
    - 실행중인 프로세서, cpu, ram 등의 시스템 정보가 저장됨
- swap
    - ram 이 가득차 공간이 부족할 때 예비 공간의 역할을 함

## Storage on phyical hard disk

### dynamic allocated

- 이름 그대로의 동적 할당
- 데이터가 늘어날 수록 할당 크기가 증가
- 한번 증가된 크기는 줄어들지 않는다
- 공간을 동적으로 할당해 공간효율이 좋으나 속도가 느림

### fixed size

- 가상머신의 크기를 고정
- 속도는 빠르나 불필요한 공간을 점유하고 있을 수 있어 공간효율이 않좋음

## LVM - logical volume manager

1. PV - physical volume
    - 물리적인 볼륨
    - 실제 디스크
2. PE - physical extention
    - PV 를 일정한 크기 - 4MB - 로 나눈 것
    - 후에 LE 와 1 : 1 대응된다
3. VG - volume group
    - PV 들의 집합
    - 여기서 다시 LV 로 할당 해 준다
4. LV - logical volume
    - 실제로 사용자가 다루게 되는 논리적인 저장소
5. LE - logical extention
    - PE 와 1 : 1 대응되는 4MB 의 구성단위
    - LV 를 구성하는 단위

### LVM 구성

- pv 를 모아 vg 로 구성한 뒤 원하는 만큼 분할하여 각각의 lv 를 만든다
- 이때 pe 와 le 는 1 : 1 대응되어진다
- 파일 시스템에서는 마지막의 나누어진 lv 를 각각의 드라이브로 사용한다

[[42/born2beroot/LVM.png]]

- 용량의 부족으로 추가 용량 확보가 필요할 시 새로운 디스크를 추가한 후 vg에 편입
- 용량이 부족한 드라이브에 용량을 추가해 주는 것 만으로 확장을 할 수 있다
- 유연한 디스크 관리가 장점

## ssh

ssh 작동상태 확인하기 `systemctl status ssh`

/etc/ssh/sshd_config 안의 설정 변경 - SSH 는 4242 port 로만 작동한다.

- Port 4242 - 보안상 이유때문에 ssh를 루트로 연결하면 안된다 - PermitRootLogin no `systemctl restart ssh`

아이맥에서 ssh 연결 - `ssh dongyoki@localhost -p 4242`

키쌍으로 연결하기 - 서버에 공개키 전달 - `~/.ssh/authorized_keys` 폴더에 공개키 넣기

### 작동원리

- Secure shell
    - shell : 명령어와 프로그램을 사용할 때 쓰는 인터페이스
    - 커널과 사용자 간의 다리 역할을 해 준다
- 네트워크 통신의 암호화 방식
- 비대칭 키 방식
    - 개인키와 공개키의 한쌍
    - 키를 생성한 쪽이 개인키를 보유하고 통신을 하려고 하는 쪽에 공개키를 넘겨준다
    - 공개키로 암호화 한 데이터는 개인키로만 복호화 할 수 있다
    - 공개키로 암호화 한 데이터를 넘겨받아 개인키로 복호화해 확인이 되면 통신이 연결된다
- 대칭 키 방식
    - 통신이 한번 연결되면 이후는 대칭키를 사용한다
    - 하나의 키를 양쪽에서 가지고 있으며 해당키로 암호화, 복호화를 한다
    - 해당 키는 매번 연결때 마다 새로 생성되며 사용이 끝나면 삭제된다

## ufw

- Uncomplicated FireWall, 복잡하지 않은 방화벽
- 기존에 많이 쓰이던 IPTables 방화벽의 프론트엔드로 간단한 방화벽 제어를 위한 툴
- 방화벽
    - 내 외부의 네트워크 트래픽을 감시하며 제어해 주는 툴
    - 신뢰할 수 있는 네트워크와 신뢰할 수 없는 네트워크간의 장벽을 만들어준다
    - 신뢰 수준이 낮은 네트워크에서 기존의 네트워크에 바로 접근할 수 없도록 만들어준다

```
apt-get install ufw
ufw status verbose // 현재 상태 확인
ufw enable // 방화벽 켜기
ufw disable // 방화벽 끄기

ufw show raw // 기본정책 보기
ufw default deny // 기본정책 차단
ufw default allow // 기본정책 허용

ufw allow <port num> // 해당 포트번호 허용
ufw allow <port num>/tcp // tcp 에서 해당포트만 허용
ufw allow <port num>/udp // udp 에서 해당포트만 허용
ufw deny <port num> // 해당 포트번호 차단
ufw deny <port num>/tcp // tcp 에서 해당포트만 차단
ufw deny <port num>/udp // udp 에서 해당포트만 차단

ufw delete deny 22 // 22번 포트가 차단되었다고 가정할때 해당룰을 삭제

ufw allow/deny ssh // ssh 라는 이름의 서비스명을 허용, 차단
less /etc/service // 서비스 보기

ufw allow/deny from <ip address> // 해당 주소로부터의 접속 허용
```

각각의 명령어들을 조합하여 사용 가능

## sudo

- super user do
- etc/sudoser 에 설정이 들어있으며 visudo 로 열 수 있다
- 윈도우의 관리자 권한으로 열기 와 비슷함
- 권한 부여에 대해서는 visudo 에 설정이 되어있다

```
apt-get install sudo
adduser dongyoki sudo
```

`sudo update-alternatives --config editor` visudo 에디터 바꾸기

visudo 내에서 작업 앞에 `Defaults` 붙이기 - sudo 를 사용한 인증은 비밀번호가 올바르지 않을경우 3회로 제한한다 - passwd_tries=3 - sudo 를 사용한 인증이 잘못된 비밀번호로 인해 오류가 발생하면 내가 선택한 사용자 정의 메세지가 나와야 한다 - badpass_message=“message” - authfail_message=“message” - sudo 를 이용한 각각의 작업은 입, 출력 모두 아카이브 되어야 한다 - log_input - log_output - log 파일은 /var/log/sudo/folder 에 저장된다 - iolog_dir=“/var/log/sudo/” - tty 모드는 보안의 이유로 활성화 되어야 한다 - requiretty - tty 가 아닌 다른 곳에서 sudo 명령어를 사용하지 못하게 하는 옵션 (ex cron) - sudo 경로 제한 - secure_path=“/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/bin:/snap/bin” - TSID - sudo 를 사용하면 해당 로그는 /var/log/sudo 에 TSID= 이라는 접두사가 붙은 디렉토리에 저장이 된다 - 해당 TSID 는 36 진법 6자리 숫자로 이루어져 있으며 2자리씩 나뉘어서 하위디렉토리로 저장이 된다

## virtual machin

- 가상화
    - 단일 물리 하드웨어 시스템에서 여러 환경을 생성하는 기술
    - 하이퍼바이저가 하드웨어의 리소스를 분리하여 독립된 하나의 가상환경을 만들어줌
    - 하이퍼바이저는 머신의 리소스를 하드웨어에서 분리한 후 vm에 배포
    - 할당된 리소스는 하이퍼바이저에 의에 vm에 맞게 파티셔닝
- hypervisor (가상 머신 모니터, vmm)
    - 가상머신을 생성, 구동하는 소프트웨어
    - 운영체제와 가상머신의 리소스를 분리하여 vm의 생성과 관리를 지원
    - 하이퍼바이저가 설치되어 사용하는 물리 하드웨어 - 호스트
    - 리소스를 사용하는 각각의 vm - 게스트
    - cpu, 메모리, 스토리지등의 리소스를 처리해줌
    - 다른 운영체제를 나란히 구동할 수 있게 해 준다
- hypervisor type
    - 유형 1
        - 베어메탈에 기반
        - vm 리소스를 하이퍼바이저가 하드웨어에 직접 예약
        - kvm (커널 기반 가상 머신), hyper-v 등이 이에 해당
            - kvm : 리눅스을 베어메탈 하이퍼바이저로 변환
    - 유형 2
        - 해당 하이퍼바이저는 호스팅되어짐
        - vm 리소스는 호스트의 운영체제에 따라 예약된 후 하드웨어에 대해 실행
        - vmware, virtualbox 가 이 에 해당
- 가상머신의 특장점
    - 단일 기기에 다중 os를 설치할 수 있음
    - 각각의 vm 은 분리되어있어 상호간에 방해를 하지 않음

## debian vs centos

- 공통점
    - linux 를 커널로서 사용, linux arm64 아키텍쳐의 도움을 받음
    - 무료이며 기업의 도움이 아닌 커뮤니티의 도움을 받아 개발, 개선, 패치되어 속도가 느림
- centos
    - redhat 계열의 RHEL 의 무료버전
    - redhat 의 유료 배포판인 RHEL 를 로고만 바꿔 출시
    - RHEL 과 완전 동일
    - yum, dnf 라는 패키지 관리자를 사용
    - 기본으로 XFS 라는 파일시스템을 사용
    - 메이저 업제이트는 적으나 버그픽스와 같은 마이너업데이트가 자주있음
    - 10년 이상 버전이 변하지 않음, 새로운 버전으로의 이동이 쉽지 않음
    - 사실상 redhat 의 RHEL 을 그대로 사용하는것
    - 기업이나 학교등의 안정성을 보는 규모있는 곳에서 많이 사용
    - 장점
        - 버전 출시가 느리지만 해당버전 내에서는 안정적임
    - 단점
        - 버전 업이 힘듬, 버전업보다 새 버전의 새 설치가 권장될정도
        - 업데이트된 일부 프로그램은 오래된 centos에서 작동하지 않을 수 있음
        - debian 보다 적은 파일시스템과 아키텍쳐 지원
        - centos 8 의 지원이 작년 12월에 끝이남
- debian
    - apt 를 패키지 관리자로 사용
    - 기본으로 EXT4 라는 파일 시스템을 사용
    - centos 에 없는 ZFS 파일시스템이 존재
    - 매 2-3년마다 버전 업데이트가 있었음, 새로운 버전으로의 이동이 쉬움
    - 장점
        - 유저 친화적인 인터페이스, 빠른 성장
        - 많은 라이브러리와 파일시스템, 아키텍쳐 지원
        - 많은 업데이트로 안정적
        - 커스텀이 쉬움
        - 업데이트가 쉬움
    - 단점
        - 기업친화적이지 못함
        - 매 업데이트가 안정적이지는 않음
        - 지원이 주로 커뮤니티를 통함
        - centos 와달리 기업의 도움을 받지 못함

## Apt vs Aptitude

- apt - advanced packaging tool
    - 소프트웨어의 설치, 제거를 관리하는 툴
    - 사용 목적에 따라 .deb 파일을 추가, 제거해준다
    - 인터페이스 없이 cli로 사용
    - 온라인 레포에서 패키지를 다운, 설치하게 된다
    - 데비안 패키지 관리 시스템인 dpkg 의 프론트 엔드로 고안되었으며
    - apt-rpm 을 통해 rpm(redhat package manager) 또한 동작할 수 있게 되었다
- aptitude
    - apt 를 더 쉽게 사용할 수 있도록 해주는 apt 의 프론트엔드 툴
    - rpm 도 지원
    - apt 의 모드기능 포함
    - apt 와 다르게 삭제시 종속관계의 패키지도 삭제
    - apt 와 다르게 설치중 충돌시 해결 제시
    - 대화형 인터페이스로 더 쉽게 사용 가능
- dpkg > apt > aptitude 순으로 high level manager

## SELinux vs AppArmor

### DAC vs MAC

- DAC (Discretionary Access Control, 임의적 접근 통제)
    - 전통적인 linux 와 unix 에서 사용
    - 파일과 프로세서에 소유자가 있어 소유자가 사용자, 또는 그룹이 파일에 접근하도록 허용
    - root 사용자는 dac 시스템 전체 접근 권한을 가짐
    - 각각의 사용자마다 접근권한을 따로 부여
    - 장점
        - 세분화된 접근권한 부여
    - 단점
        - 시스템 차원에서의 일괄적인 접근제어 부족
- MAC (Mandatory Access Control, 강제적 접근 통제)
    - 미리 정해진 정책과 보안등급에 따라 접근 통제
    - 디렉토리의 dac 설정이 변경되더라도 다른 사용자나 프로세스에 의해 엑세스를 막을 수 있음
    - 관리자 만이 접근제어의 규칙을 변경할 수 있음 (중앙집권형)
    - 다중수준의 보안정책 기반 (등급을 분류하여 데이터를 각각 보호)
    - 장점
        - 엄격한 보안
        - 다수의 파일에 대한 관리 용이
    - 단점
        - 구현과 운영의 복잡성
        - 모든 접근에 대해 확인하게 되므로 성능의 저하

### SELinux (Secuirty-Enhanced Linux)

- 미국 국가 안보국에서 보안이 취약한 linux 를 이용하기 위해 LSM (linux security module)을 이용해 만든 보안용 아키텍쳐
- Redhat 계열의 운영체제인 RHEL, Centos 에서 사용됨
- 시스템 전체에 대한 보안설정 가능
- 각각의 inode 번호로 객체를 판단
- MAC 적용
- 주체 : 사용자, 프로그램, 프로세스등 접근하는 쪽
- 객체 : 파일, 소켓, 디렉토리, 장치 등 접근 당하는 쪽
- 레이블 : 객체에 붙어있는 메타데이터
- 정책 : 주체롸 객체에 대한 접근 권한 구조를 정의
- 주체가 객체에 접근할때 적용된 정책에 맞게 접근 여부를 판단한다
- 객체에는 레이블이 붙어 객체의 속성을 알려준다

### AppArmor (application armor)

- 프로그램별로 각각의 프로그램의 역량을 제한
- LSM 커널을 이용해 구현
- 각각의 프로그램에 접근하는 경로를 확인하여 판단
    - selinux 에서와 다른부분
    - 하드링크로 생성된 파일의 경우 selinux에서는 같은 파일로 간주하나 apparmor 에서는 다른파일로 간주에 접근이 가능해짐
- MAC 적용
- 개별 응용프래그램을 보호하는 일에 집중되어있으며 각각 프로그램단위의 보안이 구현되어있다

### AppArmor 설치

설치 명령어 `apt-get apparmor apparmor-profiles apparmor-utils` 확인

```

## login policy

암호 강화 라이브러리
```

apt-get -y install libpam-pwquality sudo apt install libpam-cracklib ``` vim /etc/login.defs - PASS_MAX_DAYS = 30 - PASS_MIN_DAYS = 2 - PASS_WARN_AGE= 7

vim /etc/security/pwquality.conf 또는 vim /etc/pam.d/common-password - minlen = 10 - maxrepeat = 3 - lcredit = -1 - dcredit = -1 - difok = 7 - usercheck = 0 - reject_username - enforce_for_root - root 는 이전 암호를 묻지 않는다, 즉 이전 비밀번호에 포함되는~ 정책에서 논외이다

기본적으로 연속된 알파벳, 숫자같은 제한이 걸려있다

현재 패스워드 정책 수정 `chage -M 30 -m 2 -W 7 username`

계정의 패스워드 정책 확인 `chage -l username`

계정의 패스워드 수정 `passwd -e username`

## port from DHCP

ss -tunlp 에서 dhcp client 지우기 (68포트) - t : TCP socket - u : UDP socket - n : see number instead of host - l : listening socket - p : process using socket - 즉 사용중인 모든 port 를 보여준다 DHCP 대신 Static IP로 설정

ip route 명령어 - 현재 할당된 ip와 gateway 확인

[[42/born2beroot/ip route.png]]

gateway : 10.0.2.2 ip : 10.0.2.15

vim /etc/network/interfaces

[[42/born2beroot/interface.png]]

- DHCP 부분을 주석처리 한 뒤
- IP 와 gateway 부분 수정
- ifdown, ifup, reboot 한 뒤 ss로 재확인

## Group and User

유저 목록 `cat /etc/passwd` `grep /bin/bash /etc/passwd`

로그인한 사용자 확인 `lslogins`

그룹목록 `cat /etc/group`

### 그룹

생성 `groupadd user42` 삭제 `groupdel user42` 이름변경 `groupmod -n user-mod user42` user42 -> user-mod 유저 추가 1. `usermod` - 옵션 - G : 유저를 그룹에 속하게 만듬 - a : 기존에는 1개의 그룹에 밖에 속하지 못함, 추가로 2차 그룹을 지정할 때 사용 - `usermod -aG sudo,user42 donyoki` 2. `gpasswd` - 추가 `gpasswd -a dongyoki user42` - 삭제 `gpasswd -d dongyoki user42` 유저가 속한 그룹 확인 1. `groups dongyoki` 2. `id dongyoki`

### 유저

login 중인 유저 변경 `su username` 이전 사용자로 돌아가기 `exit`

생성 `useradd friend` - 옵션 - m : 홈 디렉토리도 함께 생성 - g : 그룹 지정 - d : 디렉토리 지정 - s : 쉘 지정 - p : 암호 지정 비밀번호 생성 `passwd friend`

생성 `adduser friend` - 비밀번호설정, 홈 디렉토리 생성등의 과정을 알아서 해줌

삭제 `userdel friend` - 옵션 - r : 홈 디렉토리 삭제

삭제 `deluser friend` - 옵션 - --remove : 홈 디렉토리 삭제 - --remove-all-files : 홈 디렉토리를 포함한 계정명으로 된 모든 것 삭제

수정 `usermod friend`

### hostname

확인 - `hostnamectl` 변경 - `hostnamectl set-hostname newname`

## cron

unix 기반 컴퓨터의 time based job scheduler

`crontab` - 옵션 - l : 리스트 보기 - r : 명령어 지우기 - e : 명령어 추가

`cron` service cron start : 크론 시작 service cron stop : 크론 중지 service cron status : 작동확인 service cron restart : crontab 수정후…재시작 systemctl disable cron : 크론 끄기(재시동해도 꺼짐) systemctl enable cron : 크론 끄기(재시동해도 꺼짐)

- * * * * 1. 매시 n분 2. 매일 n시 3. 매월 n일 4. 매년 n월 5. 매주 n요일 (일-토 <=> 0-6)
- : 모든 값 ? : 특정한 겂이 없음 - : 범위 (ex) 8-10) , : 특정 때 - ex) ‘시’ 자리에 1, 3 이면 매일 1시 3시에 / : 매 n 마다 - ex) ‘분’ 자리에 */5 라고 되어있으면 5분에 한번 L : ’일’에 사용하면 마지막일, ’요일’에 사용하면 마지막 요일(토요일)

## script

[[42/born2beroot/script.png]]

- uname : 시스템 정보 출력, 주로 시스템과 커널 정보
- nproc : physical processor 갯수 출력
- free : ram 정보
- df : disk free, disk 정보
- mpstat : sysstat 패키지에 포함, 프로세서의 정보
- who : 사용자 출력
- lsblk : 리눅스의 스토리지 정보 출력
- ss : socket statistics, 네트워크, 소켓상태 확인
    - 소켓 : 데이터를 송, 수신 하기위한 창구
    - 프로토콜, ip주소, 포트넘버로 정의되어짐
- journalctl : journal 이라는 바이너리 형식으로 저장되어진 로그데이터를 검색, 조회할 수 있는 명령어
    - TSID : sudo 로 사용 된 기록들은 설정한 /var/log/sudo 에 6자리 36진수로 된 디렉토리에 기록이 되며, 이 숫자를 TSID 라고 한다
    - journalctl 에서 TSID 로 grep 을 하면 sudo 로 실행 된 명령어들의 기록이 나오며 해당 줄의 수를 가져오면 된다

## PID, tty

PID - process id - 실행중인 프로세스의 번호

tty - teletypewriter - 콘솔, 터미널을 의미 - 콘솔 - 컴퓨터를 조작하는 입출력 장치 - 터미널 - 콘솔을 조작하는 어플리케이션 - 과거에 하나의 컴퓨터에 여러대의 tty가 연결되어있어 각각의 tty에서 로그인 후 사용하였다 - 지금은 각각의 tty가 일종의 가상콘솔을 emulate 하고있다

## systemctl, service, demon

daemon - 리눅스를 가동하면 실행되는 백그라운드 프로그램 - 사용자의 요청을 받아 대응해 준다 - 서비스 라고 부르기도 함 - 1번 프로세스(PID=1)인 systemd 의 관리하에 있다 - 데몬 프로그램의 명령어들은 끝에 d 가 붙어있다 (sshd…)

service - 리눅스 에서는 데몬을 service 파일로 설정하여 관리하고 있다 - 기존의 service 를 조작하기 위한 명령어

systemctl - systemd 를 관리하는 명령어 - 다른 데몬들도 systemd 관리하에 있기때문에 service 대신 사용할 수 있다 - service 를 사용하면 systemctl 로 리다이렉팅된다

## web server

wordpress, lighttpd, mariadb 에 대한 설명 - wordpress - 오픈소스의 설치형 블로그, 콘텐츠 관리 시스템(cms) - php 로 제작되었으며 db로는 mysql, mariadb 가 사용된다 - mariadb - 오픈소스 관계형 데이터베이스 관리 시스템 - 행, 열에 기반하는 테이블 형식의 데이터베이스 - mysql 과 같은 소스코드 기반 - lighttpd - 경량 웹 서버 애플리케이션 - fast cgi - cgi - 정적이던 서버가 동적이 되면서 php, python 등의 언어를 사용하기 시작 - 웹서버는 해당 언어를 모르기 때문에 해당 언어를 해석하는 다른것이 필요 - 웹서버와 해당 언어를 해석하는 인터프리터 사이에서 데이터를 전송, 처리해주는 것이 cgi - 요쳥이 들어올때 마다 프로세스를 생성하며 많은 요청이 들어오면 부하가 걸림 - 각각의 요청에 프로세스를 생성하는 것이 아닌 하나의 큰 프로세서에서 전부 처리하도록 만든게 fast cgi - 웹서버와 php서버를 하나로 만드는 것이 아닌 소켓통신으로 분리하여 php 서버를 따로 만들 수 있는데 이것을 WAS (web application server) 라고 함