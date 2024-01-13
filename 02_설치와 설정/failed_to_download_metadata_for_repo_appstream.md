# Failed to download metadata for repo appstream 오류 해결 방법

## 에러

```
Failed to set locale, defaulting to C.UTF-8
CentOS Linux 8 - AppStream                       50  B/s |  38  B     00:00
Error: Failed to download metadata for repo 'appstream': Cannot prepare internal mirrorlist: No URLs in mirrorlist
```

CentOS 8버전에서 `yum update` 실행 시 에러 문구가 발생한다. <br/>
이는 CentOS 8버전이 2021년 말에 종료가 되어 Mirror Site가 **vault**로 전환되어 발생되는 문제이다.

## 해결방법

Mirror Site를 **vault**로 전환해야 해결된다.

```
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-Linux-*

sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-Linux-*
```

하지만 CentOS 8 버전이 지원 종료되었기 때문에 임시방편에 불과하다. <br/>
그렇기때문에 CentOS Stream으로 업그레이드해야 한다. ([방법](./to_centos_stream_from_centos8.md))
