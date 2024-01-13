# [Docker] CentOS 설치

## 1. CentOS image 다운로드

```
docker pull centos:8
```

## 2. CentOS image 확인

```
docker image list
```

## 3. CentOS image 실행

```
docker run -d -it --privileged=true --name mysql-centos centos:8 /usr/sbin/init
```

## 4. CentOS 초기설정

### 4.1. Container ID 확인

```
docker ps
```

### 4.2. shell 환경 접속

```
docker exec -it {CONTAINER_ID} /bin/bash
```

### 4.3. 초기 설정

Failed to download metadata for repo 'appstream' 오류가 발생할 경우 이 [방법](./failed_to_download_metadata_for_repo_appstream.md)으로 해결한다

```
# 필수세팅
yum update

# ifconfig, vim, wget
yum install net-tools vim wget

# 언어팩
yum install glibc-locale-source glibc-langpack-en
```

### 4.4. 타임존 변경

#### 4.4.1. 사용 가능한 타임존 확인

```
timedatectl list-timezones | grep Seoul
```

#### 4.4.2. 타임존 변경

```
timedatectl set-timezone Asia/Seoul
```

### 4.4.3. 타임존 확인

```
date
```

<img width="209" alt="스크린샷 2024-01-13 오후 1 09 42" src="https://github.com/dev-yoonjung/real-mysql-8.0/assets/98807166/dfbebb21-ea66-4399-bf94-7faa5a959d5a">

### 4.5. System Locale 설정 변경

```
localectl set-locale LANG=en_US.UTF-8
```
