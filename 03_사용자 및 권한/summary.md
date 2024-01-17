# 03 사용자 및 권한

## 3.1 사용자 식별

- MySQL은 사용자의 계정 뿐만 아니라 사용자의 접속 지점(클라이언트가 실행된 호스트명이나 도메인 또는 IP 주소)도 계정의 일부
  - 계정을 언급할 때 아이디와 함께 **호스트**를 함께 명시
  - 모든 외부 컴퓨터에서 접속이 가능한 사용자 계정을 생성하고 싶으면 사용자 계정의 호스트 부분을 `%` 문자로 대체
  - 같은 권한이나 계정 정보가 존재할 경우 범위가 가장 작은 것부터 먼저 선택

```
'svc_id'@'192.168.0.10' # 192.168.0.10에서만 접속 가능
'svc_id'@'%' # 모든 곳에서 접속 가능
```

## 3.2 사용자 계정 관리

### 3.2.1 시스템 계정과 일반 계정

- SYSTEM_USER 권한 유무에 따라 **시스템 게정(System Account)** 과 **일반 계정(Regular Account)** 로 구분
- 시스템 계정만 사용 가능한 기능
  - 계정 관리(계정 생성 및 삭제, 계정의 권한 부여 및 제거)
  - 다른 세션(Connection) 또는 그 세션에서 실행 중인 쿼리를 강제 종료
  - 스토어드 프로그램 생성 시 DEFINER를 타 사용자로 설정

> **스토어드 프로그램**이란?
>
> - 프로그래밍 기능을 사용해 쿼리문을 더 편하게 사용할 수 있음
> - 테이블처럼 데이터베이스 내부에 저장이 됨
>
> **종류**
>
> 1. 스토어드 프로시져: 자주 사용되고 복잡한 쿼리를 모듈화해서 저장해놓고 필요할 때마다 손쉽게 사용이 가능
> 2. 스토어드 함수(사용자 정의 함수)
> 3. 트리거: 직접 트리거를 실행시킬 수는 없고 어떠한 이벤트가 발생 시 트리거가 실행됨
> 4. 이벤트 스케줄러: MySQL 5.1 버전 이상에서 제공하는 것으로 예약 작업을 수행할 수 있음 <br/>
>    (특정 시간마다 정해진 작업을 하도록 이벤트를 설정할 수 있음)
>
> ```
> /* 오후 3시에 쇼핑몰에 가입한 회원들의 이메일로 특가 상품 할인을 한다고 발송 */
>
> SHOW VARIABLES LIKE 'event%'; -- 이벤트 스케줄러 상태 확인
> SET GLOBAL event_scheduler = ON;
> ```

### 3.2.2 계정 생성

- MySQL 8.0 버전부터 계정 생성과 권한 부여에 대해 명령어 실행 구분
  - 계정 생성: `CREATE USER`
  - 권한 부여: `GRANT`
- 계정 생성 시 다양한 옵션 설정 가능
  - 계정의 인증 방식과 비밀번호
  - 비밀번호 관련 옵션
    - 비밀번호 유효 기간
    - 비밀번호 이력 개수
    - 비밀번호 재사용 불가 기간
  - 기본 역할(Role)
  - SSL 옵션
  - 계정 잠금 여부

> **일반적으로 많이 사용되는 옵션을 가진 `CREATE USER` 명령**
>
> ```
> CREATE USER 'user'@'%'
>     IDENTIFIED WITH 'mysql_native_password' BY 'password'
>     REQUIRE NONE
>     PASSWORD EXPIRE INTERVAL 30 DAY
>     ACCOUNT UNLOCK
>     PASSWORD HISTORY DEFAULT
>     PASSWROD REUSE INTERVAL DEFAULT
>     PASSWORD REQUIRE CURRENT DEFAULT;
> ```

#### 3.2.2.1 IDENTIFIED WITH

- 사용자의 인증 방식과 비밀번호 설정
- `IDENTIFIED WITH` 뒤에는 반드시 인증 방식을 명시
  - MySQL 서버의 기본 인증 방식 사용 시 `IDENTIFIED WITH 'password'`형식으로 명시

#### 3.2.2.2 REQUIRE

- MySQL 서버에 접속할 때 암호화된 SSL/TLS 채널 사용 여부
- `REQUIRE` 옵션을 SSL로 설정하지 않더라도 Caching SHA-2 Authentication 인증 방식을 사용할 경우 암호화된 채널만으로 접속 가능

#### 3.2.2.3 PASSWORD EXPIRE

- 비밀번호 유효기간 설정

#### 3.2.2.4 PASSWORD HISTORY

- 한 번 사용했던 비밀번호를 재사용하지 못하게 설정하는 옵션

#### 3.2.2.5 PASSWORD REUSE INTERVAL

- 한 번 사용했던 비밀번호의 재사용 금지 기간 설정 옵션

#### 3.2.2.6 PASSWORD REQUIRE

- 비밀번호가 만료되어 새로운 비밀번호로 변경할 때 현재 비밀번호(변경하기 전 만료된 비밀번호)를 필요로 할지 말지 결정하는 옵션

#### 3.2.2.7 ACCOUNT LOCK / UNLOCK

- 계정 생성 시 또는 `ALTER USER` 명령을 사용해 계정 정보를 변경할 때 계정을 사용하지 못하게 잠글지 여부 결정

## 3.3 비밀번호 관리

### 3.3.1 고수준 비밀번호

- MySQL 서버의 비밀번호는 쉽게 유추할 수 있는 단어들이 사용되지 않게 **글자의 조합을 강제**하거나 **금칙어를 설정**하는 기능 제공
- MySQL 서버에서 비밀번호의 유효성 체크 규칙을 적용하려면 `validate_password` 컴포넌트를 설치한 후 사용해야 함

> **`validate_password` 컴포넌트 설치**
>
> ```
> INSTALL COMPONENT 'file://component_validate_password';
> ```
>
> **설치된 컴포넌트 확인**
>
> ```
> SELECT * FROM mysql.component;
> ```

- 비밀번호 정책
  - LOW: 비밀번호의 길이만 검증
  - MEDIUM(DEFAULT): 비밀번호의 길이를 검증하며, 숫자, 대소문자, 특수문자의 배함 검증
  - STRONG: MEDIUM 레벨의 검증을 모두 수행하며, **금칙어가 포함**됐는지 여부 검증

> **금칙어 저장 파일 등록**
>
> ```
> SET GLOBAL validate_password.dictionary_file='prohibitive_word.data';
> ```
>
> **비밀번호 정책 설정**
>
> ```
> SET GLOBAL validate_password.policy='STRONG';
> ```

### 3.3.2 이중 비밀번호

- 하나의 계정에 대해 2개의 비밀번호를 동시에 설정하는 기능
- **프라이머리(Primary)** 와 **세컨더리(Secondary)** 로 구분
  - 프라이머리 비밀번호: 최근에 설정된 비밀번호
  - 세컨더리 비밀번호: 이전 비밀번호
- 이중 비밀번호가 설정된 상태에서 배포 및 재시작 전까지는 프라이머리 비밀번호와 세컨더리 비밀번호 중 아무거나 입력해도 접속 가능
- 배포 및 재시작되면 세컨더리 비밀번호는 삭제

> **이중 비밀번호 사용 방법**
>
> ```
> /* 비밀번호를 `old_password`로 설정 */
>
> ALTER USER 'root'@'localhost' IDENTIFIED BY 'old_password';
>
>
> /* 비밀번호를 'new_password'로 변경하면서 기존 비밀번호를 세컨더리 비밀번호로 설정 */
>
> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password' RETAIN CURRENT PASSWORD;
> ```
>
> **세컨더리 비밀번호 삭제**
>
> ```
> ALTER USER 'root'@'localhost' DISCARD OLD PASSWORD;
> ```

## 3.4 권한(Privilege)

### 3.4.1 정적 권한

- MySQL 서버의 소스코드에 고정적으로 명시돼 있는 권한

| 권한 구분           | 설명                                              | `GRANT` 명령에서 특정 객체 명시 여부 |
| ------------------- | ------------------------------------------------- | ------------------------------------ |
| 글로벌(Global) 권한 | 데이터베이스나 테이블 이외의 객체에 적용되는 권한 | X                                    |
| 객체 권한           | 데이터베이스나 테이블을 제어하는 데 필요한 권한   | O                                    |
| ALL                 | 글로벌과 객체 권한 두 가지 용도로 사용 가능       | -                                    |

### 3.4.2 동적 권한

- MySQL 서버가 시작되면서 동적으로 생성하는 권한
- ex) MySQL 서버의 컴포넌트, 플러그인이 설치되면 등록되는 권한

### 3.4.3 권한 부여

- 사용자에게 권한 부여 시 `GRANT` 명령어 사용

  - 각 권한의 특성(범위)에 따라 `GRANT` 명령의 `ON` 절에 명시되는 오브젝트(DB나 테이블)의 내용이 변경되어야 함

  ```
  GRANT privilege_list ON db.table TO 'user'@'host';
  ```

- `GRANT OPTION` 권한은 다른 권한과 달리 `GRANT` 명령의 마지막에 `WITH GRANT OPTION`을 명시하여 부여

#### 3.4.3.1 글로벌 권한

```
GRANT SUPER ON *.* TO 'user'@'localhost';
```

#### 3.4.3.2 DB 권한

```
GRANT EVENT ON *.* TO 'user'@'localhost';
GRATN EVENT ON employees.* TO 'user'@'localhost';
```

#### 3.4.3.3 테이블 권한

```
GRANT SELECT, INSERT, UPDATE, DELETE ON *.* TO 'user'@'localhost';

GRANT SELECT, INSERT, UPDATE, DELETE ON employees.* TO 'user'@'localhost';

GRANT SELECT, INSERT, UPDATE, DELETE ON employees.department TO 'user'@'localhost';
```

## 3.5 역할(Role)

- MySQL 8.0 부터 권한을 묶어 역할(Role)을 사용할 수 있음

> **역할 정의**
>
> ```
> CREATE ROLE role_emp_read, role_emp_write;
>
> GRANT SELECT ON employees.* TO role_emp_read;
>
> GRANT INSERT, UPDATE, DELETE ON employees.* TO role_emp_write;
> ```
>
> **역할 활성화**
>
> ```
> SET ROLE 'role_emp_read';
>
> SET ROLE 'role_emp_write';
>
> /* 역할 자동 활성화 방법 */
> SET GLOBAL activate_all_roles_on_login=ON;
> ```
>
> **계정 생성**
>
> ```
> CREATE USER reader@'127.0.0.1' IDENTIFIED BY 'qwerty';
>
> CREATE USER writer@'127.0.0.1' IDENTIFIED BY 'qwerty';
> ```
>
> **권한 부여**
>
> ```
> GRANT role_emp_read TO reader@'127.0.0.1';
>
> GRANT role_emp_read, role_emp_write TO writer@'127.0.0.1';
> ```
