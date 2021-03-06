# Contents
1. 운영체제
2. 리눅스
3. 리눅스 주요개념

## 운영체제
* 정의
  * 사용자와 하드웨어간의 소프트웨어 기반 인터페이스
    
* 목적
  * 프로그램의 실행을 제어
  * 리소스 / 장치와의 연동 및 관리
	
* 특징 및 주요개념
	1. 쉘
		* 사용자와 운영체제간의 인터페이스
	2. 장치 관리
		* 시스템 주변장치인 키보드, 모니터 등을 관리
  3. 파일 관리
		* 파일, 디렉토리에 대한 관리
  4. 메모리 관리
		* 프로그램을 메모리에 적재
		* 프로그램 간 독립적인 공간을 갖도록 관리
	5 처리기 관리
		* CPU 처리를 효과적으로 관리

## 리눅스
1. 리눅스의 디렉토리 구조
> <a href="#"><img src="https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/linux_directory_structure.png"></a>
	
2. 정의
	* 유닉스 기반 ( POSIX 표준 ) 오픈소스 운영체제
3. 목적
	* 오픈소스 운영체제
4. 특징 및 주요개념
	1. 다중 사용자 및 다중 처리 시스템
		*  하나의 시스템에 다수의 사용자들이 동시에 접속하여 사용할 수 있고(Multi-User), 각 접속자들은 다수의 응용 프로그램을 실행할 수 있다.(Multitasking)
	2. 완전히 공개된 시스템
		*  운영체제의 핵심인 커널(Kernel) 뿐만 아니라, 같이 내장되어 배포되는 대부분의 응용 프로그램이 소스가 공개된 프로그램이다.
	3. 뛰어난 네트워크 환경
		*  가장 널리 쓰이는 이더넷(Ethernet), SLIP, PPP, ATM 등의 다양한 네트워크 환경을 지원하여 TCP/IP, IPX, AppleTalk 등 대부분의 네트워크 프로그램을 지원한다.
	4. 다양한 파일 시스템 지원
		*  DOS의 FAT, Windows의 FAT32 및 NTFS, CD-ROM에 사용되는 ISO 9660, OS/2의 HPFS, SCO 및 제닉스 등의 상용 유닉스, 파일 시스템을 지원한다.
	5. 뛰어난 이식성
		*  리눅스는 약간의 어셈브리와 대부분의 C언어로 작성되어 있다. C를 컴파일 할 수 있으며, 어셈블리 부분만 새롭게 만들고 C부분을 다시 컴파일 함으로써 쉽게 다른 시스템에 이식 할 수 있다.
	6. 유연성과 확장성
		*  리눅스는 상업용 유닉스(UNIX)의 모든 특성을 가지며 유닉스의 표준인 포직스(POSIX)를 준수하고 있다. 커널, 장치드라이버, 라이브러리, 응용프로그램, 개발도구 등 리눅스의 원시코드를 쉽게 접할 수 있다.
	7. 뛰어난 안전성과 보안성
		*  리눅스는 커널 소스가 공개되어 있어 Windows와 같은 폐쇄형 운영체제에 비해 보안상의 취약점이 쉽게 노출된 가능성은 있으나, 공개용 소프트웨어를 지지하는 수많은 전문 프로그래머들이 상용 운영체제보다 빠르게 오류 수정과 보안 관련된 패치를 발표하고 있다.
	8. 우수한 가격대 성능비
		*  기업환경에서 서버로 사용되는 유닉스를 x86 시스템에 최적화시켜 효율적인 방식으로 설계하였기 때문에 PC급 서버에서도 엔터프라이즈 급의 성능을 발휘 할 수 있다.
	9. 다양한 응용 프로그램의 제공
		*  리눅스에는 운영체제의 핵심인 커널을 비롯하여 다양한 응용 프로그램을 제공한다. 셀(Shell)과 기본 명령어, X Window 시스템(Xorg, KDE, GNOME 등의 그래픽 유저 인터페이스), 편집기(vi, emacs 등), 서버 및 클라이언트 프로그램(웹, 메일, FTP 등), 개발도구(C, C++, Java, Python 등), 보안도구(nmap, tcpdump 등), 게임 등을 포함하여 배포하고 있다.
	10. 다양한 배포판의 존재
		*  리눅스는 서버, 개발용, PC용 등 다양한 목적으로 사용 가능하고, 이에 따른 다양한 배포판이 존재한다. 국외에는 레드햇(Redhat), 데비안(Debian), 우분투(Ubuntu), 수세(SUSE) 등이 있고, 국내에는 한컴리눅스, SULinux 등이 있다.
		
---
### 리눅스 주요개념과 명령어
0. 기본 명령어
	1. 종료 
		-shutdown P +10 ( 10분후 종료 ) / shutdown -r 22:00 ( 오후 10시에 재부팅 ) / shutdown -c ( 예약된 shutdown 취소 ) / shutdown -k +15 (15분후 종료된다는 메시지 보냄, 실		제로는 종료 X )
	2. 재부팅
		- reboot / shutdown -r now / init 6
	3. 로그아웃
		- logout / exit
	4. 가상 콘솔 전환
		- CTRL + ALT + F1 ~ F6
		
	5. 런레벨 ( centos 7 이전 버전의 리눅스 부팅 환경 관련 개념 )
		- 개요     : 시스템이 가동되는 방법, init 명령어 뒤에 인자로 사용한다. ( init 6 재부팅수행 )
		- 종류     : 0 종료 / 1 복구모드 / 2~3 Multi-user(텍스트모드) / 4 사용X / 5 Graphical / 6 Reboot
		- 관련파일 : /lib/systemd/system/runlevel?.target (런레벨 목록),  /etc/systemd/system/default.target ( 현재 시스템 런레벨 )
		- 관련 명령어 : init

	6. 에디터 ( vi )
		- 행 이동   : gg ( 첫째 ) / G ( 끝 )
		- 다음 화면 : ^ ( 첫 ) / $ ( 끝 )
		- 복사 / 붙여넣기 : (숫자)yy 숫자만큼 행 복사 / p, P 붙여넣기
		- 찾기 : /검색문자열  / n ( 찾은 문자 이동 )
		
	7. 도움말 : man
	
	8. 마운트 ( CD / DVD / USB )
		- 개요   : 실제 장치를 리눅스 내 폴더와 연결시키는 과정
		- 실습 명령어
			- mount 명령어로 현재 마운트 장치 확인
			- umount /dev/(장치명) 으로 해당장치 연결 해제
---		
1. 사용자와 그룹
	1. 개념
		* 리눅스는 멀티유저 시스템으로, 사용자의 권한을 제어 및 관리할 수 있다.
	2. 관련 파일
		* /etc/passwd : 사용자명:암호:사용자ID:그룹ID:전체이름:홈디렉토리:기본셸
		* /etc/group  : 그룹이름:비밀번호:그룹ID:사용자명
		* /etc/shadow : 사용자명:패스워드:패스워드 수정일:패스워드 변경 최소일:패스워드 변경 최대일:패스워드 만료 경고기간:패스워드 파기 후 계정비활성 기간:계정만료기간:예약필드
			* root:$1$9L2L0oTwd:12751:0:99999:7 : : :
	3. 관련 명령어
		* groups / groupadd / groupmod / groupdel / gpasswd
		* useradd : 사용자 생성시 /etc/skel 디렉토리의 내용을 기반으로 사용자 파일들이 자동 생성된다.
		* usermod / userdel 
		* passwd / change
		* su - 사용자명 : 사용자전환, - 입력 시 해당 사용자의 초기화 파일 적용
		* who : 접속중인 사용자 확인
		* last : 로그인 내역, 시스템 부팅 내역 확인 ( 내부적으로 /var/log/wtmp 파일 이용함 )
	
	4. 시스템 사용자
		- root / bin ( 구동파일 관리 ) / daemon / adm (로깅관리) / lp ( 프린트 ) / gdm (gnome 관리 계정)
		
	5. PAM 모듈
		1. 개요 : Pluggable한 계정,인증,세션,암화 관리 모듈 모음.
		2. 특징
			* 시스템에 공통적인 인증방법 제공가능
			* 필요한 모듈만 부분적으로 설치 가능
		3. PAM 설정파일 구성요소
			1. module_type : auth (인증) / account(계정관리) / password / session ( 인증 전후 수행되어야할 작업지정 ) 
			2. config_flag : required / requsite / sufficient ( 이모듈만 성공하면 OK ) / optional / include
			3. Module_path : /usr/lib64/security 내 사용할 모듈이름 지정
			4. Module_argument : 모듈에 전달하는 매개변수값, debug(시스템로그에 디버그정보 저장), no_warn(경고메시지 보내지않음)
		4. PAM 관련 파일 : /etc/pam.d/su, pwd, sshd
		
---
2. 파일 / 디렉토리
	1. 파일 및 디렉토리란
		* 리눅스의 모든 것은 파일로 관리된다. ( 장치, 네트워크 소켓, 일반 파일 등 )
		
	2. 파일의 종류
		1. 일반파일
			* 실행파일, 이미지, 일반 바이너리 파일, 숨김 파일
		2. 디렉토리
			* 해당 디렉토리, 하위 디렉토리 정보들이 저장.
		3. 심볼릭 링크
			* 바로가기 개념. 내부적으로 원본 파일의 정보를 저장.
		4. 장치 파일
			* /dev 디렉토리 안에 장치를 제어하는 장치 파일
	
	3. 소유권과 허가권
		* 소유권 : 리눅스의 파일의 파일은 사용자/그룹 에 대해서 소유되어진다.
		* 허가권 : 리눅스의 파일은 특정 유저, 그룹, 기타 유저에게 [ 읽기 / 쓰기 & 삭제 / 실행 ]의 허가권을 관리할 수 있다.
	
	4. 리눅스 파일 비트
		* 특수권한 파일비트( setuid | setgid | sticky bit ) | 유저권한 ( rwx ) | 그룹권한 (rwx) | 기타유저권한 (rwx) 
			* 특수권한 파일비트
				* setuid     : action 시에, 사용자의 EUID가 해당 파일의 소유자로 EUID가 변경된다. 
				* setgid     : action 시에, 실사용자 그룹의 EGID를 해당 파일의 소유자 그룹의 EGID로 변경된다.
				* sticky bit : **파일 삭제**시, 실제 소유주 혹은 root만 삭제할수 있도록 해주는 bit. (허가권이 있어도 쉽게 삭제할 수 없도록)
	
	5. 파일 링크
		* 하드링크    : 원본 파일의 inode 자체를 복사한다.
		* 심볼릭 링크 : 원본 파일의 데이터를 가리키는 포인터를 복사한다. (inode 별개)
		
	6. inode
		* 디렉토리 블록에서 아이노드 넘버를 통하여 데이터를 찾아나가는 과정
		> <a href="#"><img src="https://t1.daumcdn.net/cfile/tistory/247D5C44578F7C3116"></a>
		
		* 파일접근 시 inode의 역할
			* inode는 리눅스 파일시스템에서 사용하는 자료구조.
			* 디렉토리 정보, 심볼릭 링크 개수, 파일 사이즈, **실제 파일 데이타블록**의 주소 등 메타데이타를 가지고 있다.
			* 동작 과정: 사용자는 파일이름으로 파일에 ACTION => 디렉토리 블록에서 해당 파일 INODE 리턴 => INODE 테이블에 저장된 파일의 실제주소를 통하여 파일을 불러온다.
			
3. 리눅스 관리자용 명령어
	***
	1. RPM
		1. 개념 : RedHat PACKAGE MANAGER, 설치 파일 ( .rpm ) 을 실행.
		2. 특징
			- RPM 파일 이름 형식 : 패키지이름-버전-릴리즈번호.CentOS버전.아키텍처.rpm
			- DNF(yum)에 비해, 패키지 내 의존성 문제가 발생 시, 파악 및 설치가 어렵다.
		3. 주요 명령어
			- 설치 : rpm -Uvh 패키지명 : U ( 설치 or 업데이트 ) / v ( 설치과정 확인 ) / h (설치 진행 과정 출력 )
			- 삭제 : rpm -e 패키지명
			- 설치된 패키지 조회 : rpm [ -qa(설치여부) | -ql(포함파일확인) | -qi(상세정보) ] [ 패키지명 ] 
			- 미설치된 패키지 상세 조회 : rpm -qip 패키지명
	***
	2. DNF(YUM)
		1. 개념 : RPM의 의존성 문제를 해결하는 상위호환 패키지 매니저.
		2. 특징
			- RPM 패키지를 설치, 의존성 있을 시 필요한 의존성까지 자동으로 설치
			- RPM 패키지 저장소 설정 필요
		3. DNF 명령 작동 흐름도
			> DNF INSTALL -> /etc/yum.repos.d/ 디렉토리 패키지 저장소 URL 확인 -> 패키지 저장소 탐색 및 다운로드
		4. 주요 명령어
			- 설치 
				- dnf -y --nogpgcheck install [ 패키지명 ] : 설치과정 중 모든대답 Y / GPG 키 검사 생략
				- dnf install [ rpm파일이름.rpm ]
			- 업데이트 : dnf check-update / dnf update [ 패키지명 ]
			- 삭제 : dnf remove  [ 패키지명 ] / dnf clean all ( 모든 패키지 삭제 )
			- 조회 : 
				- dnf list [ available | 패키지명 ] : 설치가능한 / 해당 패키지 목록 조회
				- dnf info [ 패키지명 ] : 패키지 정보 확인
				- dnf provides [파일] : 해당파일 속한 패키지 출력 ( ex: dnf provides ifconfig )
		5. repo 파일 형식
		```
		[DVD-BaseOS]
		name=CentOS DVD
		baseurl=file:///media/cdrom/BaseOS/
		gpgcheck=0

		[BaseOS]
		name=CentOS-$releasever - Base
		baseurl="http|ftp|file 형식 URL 주소"
		mirrorlist="대체 URL주소 : baseurl이 없을 시 mirrorlist 사용"
		enabled=1 // 이 저장소 사용
		gpgcheck=0 // 체크 안한다.
		```
		
***
	3. 파일 압축 & 해제		
	4. 파일 위치 검색
	
	---
	5. 시스템 설정 ( systemd )
		- 개요
			- unit이라는 구성요소로 전체 시스템을 시작하고 관리하는 프로세스 /  
			- systemd는 관리대상의 이름을 '서비스이름.유닛종류' 형태로 관리
		- 유닛의 종류
			> <a href="#"><img src="https://blog.kakaocdn.net/dn/bBykuV/btqxPSbTLkF/nUKxCDHk0GwsRf42IkHVF1/img.png"></a>
		- 주요 유닛
			- atd.service : 예약 작업
			- get-default : 부팅 모드 설정	
	6. CRON & AT

***

4. 네트워크 관련 설정과 명령어
	1. 네트워크 필수개념
		- TCP/IP , 호스트/도메인 명, IP / 네트워크 / 브로드캐스트 / 게이트웨이 주소 / 넷마스크와 클래스 / DNS 서버주소
	2. 리눅스에서 네트워크 장치
		- 랜 카드(NIC)는 리눅스에서 eth0, eth1, ens160 등의 이름으로 인식된다.
	3. 주요 명령어
		- nmtui	
			- 자동 IP 주소 혹은 고정 IP주소 사용 설정
			- IP주소, 서브넷 마스크, 게이트웨이 정보 입력
			- DNS 정보 입력
			- 네트워크 카드 드라이버 설정
			- 네트워크 장치 설정
			- systemctl [start/stop/restart/status] NetworkManager ( 네트워크 설정 변경 내용 적용 )
		- ifup ( 장치가동) / ifdown (장치중지) / ifconfig ( 장치확인 ) [ 네트워크 장치명 ]
		- nslookup : DNS 서버 작동 확인
		- ping IP/URL : 해당 IP/URL의 네트워크 응답 확인
		- route : 외부 대역과 통신 시 필요한 게이트웨이 정보를 확인 및 설정
	
	4. 주요 파일
		- /etc/sysconfig/network : 네트워크 기본 정보 및 네트워크 사용여부 확인
		- /etc/sysconfig/network-scripts/ifcfg-[장치명] : 장치 설정 확인
		- /etc/resolv.conf : DNS 서버 및 호스트 정보 확인
		- /etc/hosts : 호스트이름 및 FQDN(호스트+도메인명) 확인
		
	5. SELinux
		- 개요 : 보안에 영향을 미치는 서비스, 권한등을 제어 하는 프로그램
		** todo: 깊이 다루기
	
***
5. 파이프, 필터, 리다이렉션
	- 파이프 : 여러 프로그램(명령어)을 연결하는 연결통로
	- 리디렉션 : 표준 입출력 방향을 변경
	- '|' , '>', '>>' (기존 파일 있으면 append)
***
6. 프로세스, 데몬, 서비스
	- 주요개념 : PID, 작업번호 / 부모,자식프로세스 / 포그라운드 프로세스 / 데몬 = 서비스(백그라운드 프로세스) /
	- 주요명령어 : pstree , ps, kill, jobs(백그라운드 확인), CTRL+Z(일시중지), bg(백그라운드 전환), fg(포그라운드 전환), CTRL+C(종료)

***
7. 서비스와 소켓
	1. 서비스
		- 개요       : 시스템과 독자적으로 제공, 구동되는 프로세스. ( httpd, mysqld, vsftpd )
		- 주요 명령어 : systemctl [ start / stop / restart / status / enable / disable ] 서비스이름 / systemctl list-unit-files ( 서비스 부팅시 자동구동 및 상태 확인 )
		- 주요 서비스 : journalctl ( 서비스 로그 확인 ), 
		- 관련 파일  : /usr/lib/systemd/system/서비스명.service
		> <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile26.uf.tistory.com%2Fimage%2F993A493F5B0151991DF859"></a>
		- 서비스 관련 설명 링크 : https://victorydntmd.tistory.com/215
		
	2. 소켓
		> <a href="#"><img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcSH8fU%2FbtqvxsTPQ2E%2Fpnl61uUJOAPdf73whDlTW0%2Fimg.png"></a>
		- 개요     :외부에서 특정 서비스를 요청할 경우 systemd가 구동시키는 프로세스. 요청이 끝나면 socket도 종료된다. ( telnet )
		- 관련 파일 : /usr/lib/systemd/system/소켓명.socket

***
8. Grub & 시스템 복구

***
9.커널 컴파일

***

10. 리눅스 로그
	1. 종류
	> <a href="#"><img src="https://11q.kr/g5s/data/editor/1905/thumb-3232235521_1556728625.2615_800x673.png"></a>
	2. rsyslog
		1. 개요 : 시스템로그를 관리하는 서비스
		2. 관련파일 : /etc/rsyslog.conf
		3. syslog 파일 형식
			- [Facility].[Level] 	[Action]
				- Facility : 어떤 것들을 로그로 남길지 설정 ( kern, user, mail, daemon, auth, syslog, cron, ... )
				- Level    : 로그레벨 설정 ( emerge, alert, crit, err, warning, ... )
				- Action   : 어디에 저장할지 ( ex: /var/log/cron )
				
