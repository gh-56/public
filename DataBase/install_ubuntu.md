# 우분투에 MySQL 설치해보기
1. 우분투 서버 업데이트
```
$ sudo apt-get update
```
2. mysql-server 설치
```
$ sudo apt-get install mysql-server
```
3. 설치가 되었는지 버전 확인해보기
```
$ mysql --version
mysql  Ver 8.0.34-0ubuntu0.22.04.1 for Linux on x86_64 ((Ubuntu))
```
4. 외부 컴퓨터가 접근할 수 있도록 방화벽 설정 해주기
```
$ sudo ufw allow mysql
```
  - 방화벽 허용 했는지 확인하는 방법
    ```
    $ sudo cat /etc/ufw/user.rules

    중간쯤을 보면
    ### tuple ### allow tcp 3306 0.0.0.0/0 any 0.0.0.0/0 in
    -A ufw-user-input -p tcp --dport 3306 -j ACCEPT
    라고 작성되어 있다.
    ```
    
5. mysql 서비스 시작
```
$ sudo systemctl start mysql
```
  - 서버가 실행 중인지 확인하기 
    ```
    $ sudo systemctl status mysql
    ```
6. 상시 가동하게끔 설정 (우분투 시작 시 MySQL 자동재시작)
```
$ sudo systemctl enable mysql
```
7. MySQL 보안 설정
```
$ sudo mysql_secure_installation
```
  - 질문과 대답
    1. 패스워드 난이도를 설정할 것인지 : n
        - y 를 하면 mysql 사용자의 비밀번호를 설정할 때, 비밀번호의 난이도를 강제할 수 있다.
        - 로컬에서 공부용으로 사용할거라 n
    2. root 계정의 비밀번호를 수정할 것인지 : pass되었음
    3. 익명 사용자를 제거할 것인지 : y
    4. 원격으로 root의 mysql접속을 허용하지 않을 것인지 : y
    5. Test 데이터베이스를 삭제할 것인지 : y
    6. 권한테이블을 reload할 것인지 : y
8. MySQL 접속  
아래의 명령어를 입력하고 패스워드 입력하라고 하면 그냥 엔터 를 친다.
```
$ sudo mysql -u root -p
```
## MySQL 초기 root 패스워드 설정
```
mysql> use mysql;
mysql> select host,user,authentication_string from user;
```
명령어 입력 결과 출력창에서 root를 찾아 authentication_string을 보면 아무것도 적혀있지 않을 것이다.
```
mysql> alter user 'root'@'localhost' identified with mysql_native_password by '1234';
```
로컬호스트의 root 비밀번호를 1234로 지정하는 명령어
```
mysql> select host,user,authentication_string from user;
```
다시 root 확인해보면 authentication_string이 추가되어 있음

## 샘플 데이터베이스 추가하기
```
$ wget http://download.hanbit.co.kr/mysql/8.0/employees.zip
$ unzip employees.zip

압축 푼 경로에서 mysql 접속하기
mysql> source employees.sql
...
...
완료되면
mysql> show databases;

+--------------------+
| Database           |
+--------------------+
| employees          |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
```

## Workbench 설치하기
```
$ sudo apt-get install mysql-workbench-community
```
만약 의존성에 관한 출력이 나온다면,
```
$ sudo apt-get install -f
```

## connectorJ 설치
mysql 홈페이지에서 connectorJ deb 다운로드
```
$ dpkg -i 다운받은 커넥터 deb
```
다운받으면 /usr/share/java에 connector-java 파일 생김

### 참고
> 이것이 MySQL이다 개정판 - 한빛미디어, 우재남 지음  
> [[Ubuntu] 우분투에 MySQL 설치하기](https://velog.io/@seungsang00/Ubuntu-%EC%9A%B0%EB%B6%84%ED%88%AC%EC%97%90-MySQL-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)  
> [MySQL 설치하기 – 우분투 22.04 LTS](https://smoothiecoding.kr/mysql-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0-%EC%9A%B0%EB%B6%84%ED%88%AC-2204lts/)  
> [우분투(Ubuntu) 환경에 방화벽(UFW) 설정하기](https://lindarex.github.io/ubuntu/ubuntu-ufw-setting/)  
> [Ubuntu mysql 설치하기](https://velog.io/@juhyeon1114/Ubuntul-mysql-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)  
> [MySQL 초기 root 패스워드 설정](https://indienote.tistory.com/585)