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
docker run -d -it --name centos centos:8
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

```
# 필수세팅
yum update

# ifconfig, vim, wget
yum install net-tools vim wget

# 언어팩
yum -y install glibc-locale-source glibc-langpack-en
```
