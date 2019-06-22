---
layout: post
title: "GeoIP Auto Update"
subtitle: GeoIP 자동 업데이트 구성하기
date: 2019-01-06 12:00:00 +0900
categories: Shell
tags: GeoIP
---

## GeoIP 자동 업데이트 구성
GeoIP 데이터베이스 파일을 이용하면 접근한 IP 주소의 국가, 도시 등의 정보를 얻을 수 있다.    
또한, 갱신되는 파일을 주기적으로 업데이트해 줄 필요가 있으므로 자동으로 업데이트 가능하도록 해보자.

---

### GeoIP update 설치
[geoipupdate](https://github.com/maxmind/geoipupdate/releases) 다운로드 링크에서 
[geoipupdate-3.1.1.tar.gz](https://github.com/maxmind/geoipupdate/releases/download/v3.1.1/geoipupdate-3.1.1.tar.gz) 파일을 다운로드하여 해당 경로에서 아래 명령들을 순차적으로 수행한다.

```bash
$ tar -zxvf geoipupdate-3.1.1.tar.gz
$ cd geoipupdate-3.1.1
$ ./configure
$ make
$ make install
```

명령 실행중 `curl` 이나 `zlib` 이 설치되어 있지 않으면 에러가 발생하므로 설치 후 다시 명령을 수행하면 된다.
```bash
# curl 설치
$ yum install curl-devel

# zlib 설치
$ yum install zlib-devel 
```

---

### GeoIP.conf 설정
GeoIP.conf 파일에 계정 정보와 필요한 에디션을 설정한다.  
유료 이용자에게는 GeoIP2와 GeoIP Legacy DB를 지원하고, 무료 이용자에게는 GeoLite2 DB만 지원한다. ([유료 이용 라이센스 발급](https://www.maxmind.com/en/accounts/current/license-key/GeoIP.conf))

*/usr/local/etc/GeoIP.conf*
```bash
# Paid
AccountID YOUR_ACCOUNT_ID_HERE
LicenseKey YOUR_LICENSE_KEY_HERE
EditionIDs YOUR_EDITION_IDS_HERE

# Free
AccountID 0
LicenseKey 000000000000
EditionIDs GeoLite2-City GeoLite2-Country
```
 
---
 
### GeoIP update 실행
아래 명령을 수행하면 /usr/local/share/GeoIP 경로에 설정한 Edition이 생성된다.  
예를 들어, EditionIDs GeoIP2-City 이면 `GeoIP2-City.mmdb` 파일이 생성된다.  
(단, 방화벽을 사용하는 경우에는 DNS와 443포트를 열어줘야 한다.)

```bash
$ /usr/local/bin/geoipupdate
``` 

실행 스크립트를 `crontab` 에 설정하여 주기적으로 동작시키고 결과 값을 메일로 전송할 수 있다.
```bash
MAILTO=mirotic91@github.com
2 22 * * 4 /usr/local/bin/geoipupdate2
```

---

### GeoIP 자동 업데이트
자동으로 업데이트 받는 shell 을 간단히 작성했다. 실행하면 최신 `GeoIP2-City.mmdb` 파일을 원하는 경로로 이동시킨다.  
배포 스크립트에 아래 shell 실행문을 추가해주면 최신 GeoIP를 유지할 수 있다.

```bash
#!/bin/bash

/usr/local/bin/geoipupdate
DOWNLOAD_DIR=/usr/local/share/GeoIP
TARGET_DIR=/DATA/GeoIP

mv -f $DOWNLOAD_DIR/GeoIP2-City.mmdb $TARGET_DIR/
chown -R tomcat:tomcat $TARGET_DIR
```

<br>
##### Reference
- *[maxmind](https://dev.maxmind.com/geoip/geoipupdate/)*