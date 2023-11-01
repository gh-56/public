# MySQL 설치

## 설치 할 MySQL 관련 소프트웨어
- **MySQL Server** : 서버 프로그램
  - 설치할 때 MySQL Servers -> MySQL Server -> MySQL Server 8.0 -> MySQL Server 8.0.17 -X64 선택
- **MySQL Client** : 클라이언트 프로그램
  - MySQL Server를 추가하면 안에 포함되어 있다.
- **MySQL Workbench** : GUI 지원 통합 관리 툴
  - 리눅스와 유닉스는 텍스트 기반으로 주로 사용되므로 MySQL Server, Client 두 가지로도 충분하다.
  - Application -> MySQL Workbench -> MySQL Workbench 8.0 -> MySQL Workbench 8.0.17 -X64 선택
- **Sample Database** : 샘플 데이터베이스
  - Documentation -> Samples and Examples -> Samples and Examples 8.0 -> Samples and Examples 8.0.17 -X64 선택

## MySQL 환경 설정 (Product Configuration)
- Type and Networking에서 Config Type을 **'Development Computer'** 로 선택해야 한다.
  - MySQL 외에 여러 프로그램을 사용할 때 *'Development Computer'* 선택
  - 중요한 서버가 가동되는 컴퓨터는 *'Server Computer'* 선택
    - 최소한의 메모리만 사용해서 웹 서버 등의 성능 저하를 최소화 한다.
  - MySQL 전용 컴퓨터로 사용하려면 *'Dedicated Computer'* 선택
    - MySQL이 사용 가능한 메모리를 최대한 사용해서 성능이 좋아진다.
- TCP/IP가 **3306 포트**인 것을 확인하고 **'Open Windows Firewall ports for network access'** 체크 되어있는지 확인한다.
  - 외부 컴퓨터에서 접근할 수 있도록 'Windows 방화벽'의 포트를 허용해 주는 것이다.

#### 비밀번호 설정
- root : MySQL의 모든 권한이 있는 관리자의 이름  
비밀번호는 최소 8자 이상에 문자/숫자/기호를 섞어서 만들 것이 권장된다.

## MySQL Workbench 설정
- Edit -> Preferences -> SQL Editor 에서 'Safe Updates(rejects UPDATEs and DELETEs with no restrictions)' 체크 끄기
  - 이 체크를 켜놓으면 Update문이나 Delete문의 조건에서 Primary Key가 설정된 열이 들어가야만 하기 때문이다. (원활한 사용을 위해 체크를 끈 것)

### + MySQL의 실행 파일이 있는 경로를 Path에 추가

## 설치 후 확인할 사항
설치된 폴더 확인  
  - C:\Program Files\MySQL\MySQL Server 8.0  
  - 폴더의 역할 또는 저장된 파일  

    |폴더|역할|
    |---|---|
    |bin(중요)|MySQL 서버 프로그램, 클라이언트 프로그램 및 유틸리티 프로그램 파일|
    |docs|도움말 또는 설정 파일|
    |etc|설정 파일 샘플|
    |include|응용 프로그램을 개발할 때 필요한 헤더 파일|
    |lib|MySQL 관련 라이브러리 파일|
    |share|기타 지원 파일, 각 언어별 오류 메시지 파일 등|
    |ProgramData(숨김)|데이터베이스 파일들과 로그 파일들이 들어있는 폴더|

## MySQL 제거
제어판을 통해 제거한다.

### 참고
> 이것이 MySQL이다 개정판 - 한빛미디어, 우재남 지음