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

### 2.1.2. MySQL 설치

> [[Docker] CentOS 설치](/02_설치와%20설정/docker_install_centos8.md)
