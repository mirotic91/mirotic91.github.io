---
layout: post
title: "MongoDB export"
subtitle: MongoDB csv 파일로 내보내기
date: 2019-12-18 20:38:00 +0900
categories: MongoDB
tags: MongoDB export
---

## MongoDB export

---

### 들어가기
`MongoDB` 에 있는 데이터를 `csv` 파일로 내려받아야 하는 일이 생겼다.
아직 MongoDB 입문인지라 어떻게 해야할지 찾아보니 `mongoexport` 라는 명령어를 알게되어 소개하고자 한다.  

대용량의 데이터를 내려받고자 하는 호스트에 직접 연결하여 export 하면 속도는 빠르지만 받고나서 로컬로 가져오기 벅찰 수 있다.
속도는 비교적 느리나 해당 호스트로부터 로컬에 내려받기도 가능하니 이 또한 알아보도록 한다.

---

### 명령어 소개
`mongoexport` 는 `json`, `csv` 두 가지 타입을 지원한다.  
Authentication, Replica, Shard 옵션에 대한 설명은 생략한다!

```bash
--host[-h] : hostname:port 형태로 입력한다. (localhost:27017 경우 생략 가능)
--db[-d] : db 이름을 입력한다.
--collection[-c] : collection 이름을 입력한다.
--type : json(default) or csv 출력형식을 입력한다.
--out[-o] : 출력파일을 위치시킬 경로와 파일명을 입력한다.
--fields[-f] : field1[,field2] 출력을 원하는 필드를 구분자 `,`를 이용하여 입력한다.
--fieldFile : -f 옵션 대신 출력을 원하는 필드들을 저장한 파일을 이용한다. 이때, 파일의 라인 단위로 필드명을 구분해야 한다. (csv only)
--noHeaderLine : field name 을 생략하고 순수 데이터만 출력한다. (csv only)

```

로컬 호스트에 있는 MongoDB에서 fields 옵션을 이용하여 export
```bash
$ mongoexport --db=account --collection=user \
              --fields=name,email,phone \
              --type=csv \
              --out=./account.csv

# account.csv
name,email,phone
drogba,drogba@mail.com,01033228877
ozil,ozil@mail.com,01011336699
henry,henry@mail.com,01044779911
```

다른 호스트에 있는 MongoDB에서 fieldFile 옵션을 이용하여 export
```bash
# field.txt
name
email
phone

$ mongoexport -h="mongodb.test.com:27017" \
              -d=account -c=user \
              --fieldFile=./field.txt \
              --type=csv \
              -o=./account.csv \
              --noHeaderLine

# account.csv
drogba,drogba@mail.com,01033228877
ozil,ozil@mail.com,01011336699
henry,henry@mail.com,01044779911
```

---

<br>
##### Reference
- *[Docs](https://docs.mongodb.com/manual/reference/program/mongoexport)*