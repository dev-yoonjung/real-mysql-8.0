# CentOS 8을 CentOS Stream으로 업데이트

## 1. 리포지토리 파일 설치

```
dnf install centos-release-stream -y
```

• dnf: yum과 같은 CentOS 8 버전에 추가된 패키지 관리 명령어

## 2. 시스템 업데이트

```
dnf distro-sync -y --allowerasing
```

## 3. 재부팅 후 설치된 버전 확인

### 3.1. 재부팅

```
reboot
```

### 3.2. Container 재실행

```
docker start {CONTAINER_NAME}
```

### 3.3 Container ID 확인

```
docker ps
```

### 3.4. shell 환경 재접속

```
docker exec -it {CONTINAER_ID} /bin/bash
```

### 3.5. 설치된 버전 확인

```
cat /etc/centos-release
```

<img width="332" alt="스크린샷 2024-01-13 오후 1 04 45" src="https://github.com/dev-yoonjung/real-mysql-8.0/assets/98807166/35a4be77-10d7-4e45-b0da-ccc10af84989">
