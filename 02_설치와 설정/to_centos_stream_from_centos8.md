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

```
# 재부팅
reboot

# 설치버전 확인
cat /etc/centos-release
```
