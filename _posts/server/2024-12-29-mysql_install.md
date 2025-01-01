---
layout: single
title: "AWS EC2서버에 MySql 설치순서"
---
# 1. MySql 설치 (ec2-user로 수행)
1.rpm 설치
```
$ sudo dnf install https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
```

2.GPG키 업데이트 -- 기 설치된 mysql GPG키가 맞지 않음. 업데이트 필요
```
$ sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023`
$ sudo yum update
```

3.mysql 설치
```
$ sudo dnf install mysql-community-server
```

4./var/log/mysqld.log에서 root 임시비밀번호 확인 : {임시비밀번호}
```
$ sudo vi /var/log/mysqld.log
```

5. mysql 기동
```
$ sudo systemctl start mysqld
```

6. mysql root로 접속 (임시비밀번호로)
```
$ mysql -u root -p
```

7. root 패스워드 변경
```
mysql>  ALTER USER 'root'@'localhost' identified by '{신규비밀번호}';
```
# 2. 환경설정
1. 기본환경설정 - 캐릭터셋,테이블명대소문자구분
```
$ sudo vi /etc/my.cnf  -- ec2-user로 수행
-. 캐릭터셋 : character_set_client = 'utf8mb4', character_set_server = 'utf8mb4'  --  default임,  아모티콘같은 문자도 허용
-. 테이블명소문자허용 : lower_case_table_names=1  -- 리눅스와 같이 대소문자 구분하는 시스템의 경우 허용으로 셋팅필요
mysql> show variables like 'char%'; -- 캐릭터셋 확인 -- root로 수행
```

2. database 생성 (root로 수행)
```
mysql> show databases;   -- DBMS에 생성된 database 조회
mysql> create database {DB명}; -- lms_db database 생성
```

3. 어플리케이션 사용자 생성, 권한부여 (root로 수행)
```
mysql> create user '{사용자명}'@'%' identified by '{비밀번호}';  -- user 생성
mysql> grant all privileges on {DB명}.* to '{사용자명}'@'%' with grant option;  -- xxxxx에게 xxxxx에 대한 모든 권한 부여, %는 모든 ip에서 접속허용을 의미
mysql> flush privileges; - 권한 리플레쉬
```

4. DB접속 정보
- DB서버주소 : ec2-XXXXXX.compute.amazonaws.com
- 접속port : 3306
- 접속DB : {DB명}
- 접속계정 : {사용자명}
