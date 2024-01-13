# 02 설치와 설정

## 2.1 MySQL 서버 설치

### 2.1.1 버전과 에디션(엔터프라이즈와 커뮤니티) 선택

#### 버전 선택

- 제약 사항이 없다면 가능한 한 최신 버전을 설치
- 기존 전에서 새로운 메이저 버전으로 업그레이드하는 경우, 최소 패치 15~20번 이상 릴리스된 버전을 선택
- 새로운 서비스의 경우, 메이저 버전보다 조금 더 빠른 패치 버전 선택
  - 메이저의 경우 치명적인 버그 발생 경우 존재

#### 에디션 선택

- 엔터프라이즈 에디션의 소스코드는 미공개
- 엔터프라이즈 에디션에 부가 기능 탑재
  - Thread Pool
  - Enterprise Audit
  - Enterprise TDE(Master Key 관리)
  - Enterprise Authentication
  - Enterprise Firewall
  - Enterprise Monitor
  - Enterprise Backup
  - MYSQL 기술 지원
- 엔터프라이즈 에디션과 커뮤니티 에디션의 기본 성능은 동일

### 2.1.2 MySQL 설치

> [[Docker] CentOS8 설치](/02_설치와%20설정/docker_install_centos.md)

#### 2.1.2.1 yum repository 등록

[MySQL 다운로드 페이지](https://dev.mysql.com/downloads/repo/yum/)에서 운영체제 버전에 맞는 RPM 파일을 `Download` 버튼을 우클릭해서 링크 주소를 복사한다.

<img width="1083" alt="스크린샷 2024-01-13 오후 5 47 08" src="https://github.com/dev-yoonjung/real-mysql-8.0/assets/98807166/a3265fe1-4f3f-4a08-b814-e4b9883dc6ef">

yum repository에 등록한다.

```
yum install -y {복사한 링크 주소}
```

<img width="559" alt="스크린샷 2024-01-13 오후 6 47 49" src="https://github.com/dev-yoonjung/real-mysql-8.0/assets/98807166/98f0bf2c-d9b2-430d-ba44-4d2405e5bfd3">

#### 2.1.2.2 yum repository 확인

```
yum repolist enabled | grep "mysql.*"
```

#### 2.1.2.3 mysql 설치

```
yum install -y mysql-server
```

#### 2.1.2.4 설치된 mysql 버전 확인

```
mysqld -V
```

## 2.2 MySQL 서버의 시작과 종료

### 2.2.1 시작과 종료

#### 2.2.1.1 재부팅 시 자동 시작하도록 설정

```
systemctl enable mysqld
```

#### 2.2.1.2 서비스 시작

```
systemctl start mysqld
```

#### 2.2.1.3 서비스 구동 여부 확인

```
systemctl status mysqld
```

<img width="563" alt="스크린샷 2024-01-13 오후 8 16 56" src="https://github.com/dev-yoonjung/real-mysql-8.0/assets/98807166/6d69b24f-3595-45ce-872e-0379d34bc94a">

#### 2.2.1.4 서비스 종료

```
systemctl stop mysqld
```

> **Clean shutdown** <br/>
> : 모든 커밋된 데이터를 데이터 파일에 적용하고 종료하는 것 <br/>
> ▶ 별도의 트랜잭션 복구 과정을 진행하지 않기 때문에 빠르게 시작할 수 있음
>
> ```
> # MySQL 서버
> SET GLOBAL innodb_fast_shutdown=0;
>
> # linux
> systemctl stop mysqld.service
> ```

### 2.2.2 서버 연결 테스트

```
# 소켓 파일로 접속
mysql -uroot -p --host=localhost --socket=/tmp/mysql.sock

# TCP/IP 접속(원격 접속)
mysql -uroot -p --host=127.0.0.1 --port=3306

# 로컬환경 접속
mysql -uroot -p
```

## 2.3 서버 설정

> **적용되고 있는 my.cnf(설정파일) 확인하기**
>
> ```
> mysql --help
> ```
>
> <img width="500" alt="스크린샷 2024-01-13 오후 9 09 26" src="https://github.com/dev-yoonjung/real-mysql-8.0/assets/98807166/206fee54-736f-4e00-830a-bc738bdb280a">

### 2.3.1 설정 파일의 구성

- MySQL 설정파일은 my.cnf나 my.ini 파일에 여러 개의 설정 그룹을 담을 수 있음
- 대체로 실행 프로그램 이름을 그룹명으로 사용
  - `mysqldump` 프로그램: [mysqldump] 설정 그룹 참조
  - `mysqld` 프로그램: [mysqld] 설정 그룹 참조
  - `mysqld_safe` 프로그램: [mysqld_safe]와 [mysqld] 설정 그룹 참조

### 2.3.2 MySQL 시스템 변수의 특징

- 시스템 변수(System Variables): MySQL 서버는 기동할 때 메모리나 작동 방식을 초기화하거나, 접속된 사용자를 제어하기위해 저장된 값
- [시스템 변수(Server System Variables)를 설정한 페이지](https://dev.mysql.com/doc/refman/8.0/en/server-system-variable-reference.html)에서 제공하고 있는 모든 시스템 변수의 목록과 간단한 설명을 참고할 수 있다.

### 2.3.3 글로벌 변수와 세션 변수

- MySQL의 시스템 변수 적용 범위에 따라 **글로벌 변수**와 **세션 변수**로 구분
- 일반적으로 세션별로 적용되는 시스템 변수의 경우 글로벌 변수뿐만 아니라 세션 변수에도 동시에 존재
- **글로벌 변수**: 하나의 MySQL 서버 인스턴스에서 전체적으로 영향을 미치는 시스템 변수
  - 주로 MySQL 서버 자체에 관련된 설정
  - ex) innodb_buffer_pool_size, key_buffer_size
- **세션 변수**: 클라이언트가 MySQL 서버에 접속할 때 기본으로 부여하는 옵션의 기본값을 제어하는 데 사용되는 변수
  - ex) autocommit

### 2.3.4 정적 변수와 동적 변수

- MySQL 서버가 기동 중인 상태에서 변경 가능한지에 따라 **동적 변수**와 **정적 변수**로 구분
  - **정적 변수**: 디스크에 저장되어 있는 설정 파일(my.cnf 또는 my.ini)에 설정되어있는 시스템 변수
  - **동적 변수**: 이미 기동 중인 MySQL 서버의 메모리에 설정되어 있는 시스템 변수
    - 현재 기동 중인 MySQL의 인스턴스에서만 유효

### 2.3.5 SET PERSIST

- MySQL8.0 부터 사용 가능
  - 동적 변수의 경우 `SET GLOBAL` 명령으로 반영 가능하지만 정적 변수까지 반영되지 않는 문제를 보완하기 위해 `SET PERSIST `기능이 추가됨
- **SET PERSIST**: MySQL 서버 설정 파일(my.cnf)에 변경 내용을 수동으로 기록하지 않아도 자동으로 영구 변경
- **SET PERSIST_ONLY**: 현재 실행 중인 MySQL 서버에는 변경 내용을 적용하지 않고 다음 재시작 후 반영을 위해 `mysqld-auto.cnf`파일에만 변경 내용을 기록
- **RESET PERSIST**: `SET PERSIST`, `SET PERSIST_ONLY` 명령으로 추가된 시스템 변수의 내용을 삭제
