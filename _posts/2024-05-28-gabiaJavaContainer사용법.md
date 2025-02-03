---
title: "Java Container 호스팅 서버 사용법"
date: 2024-05-28 02:23:00 +0900
categories: [Back-end]
tags: [Back-end, Spring, Java, DB, ]
---
# 1. 소개
 상시 서버를 구동시킬 여견이 없어 Gabia에서 제공하는 Java Container 호스팅 서비스를 이용하게 되었습니다.

# 2. 접속 방법
1. 서버 관리 콘솔 홈페이지에서 접속 허용 IP를 등록합니다.
2. 접속 가능 기간을 설정합니다.
3. Putty Host Name칸에 Domain 주소 또는 Outbound IP를 적고 접속합니다.
4. Console창에 접속 ID를 입력하고 미리 설정해둔 비밀번호로 접속합니다.

* root 계정으로 접속을 시도할 경우 해당 IP가 자동으로 벤됩니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/8102ee50-f0e9-41ec-ac22-a1e65199ddab" width="720" height=""/>
<p><b>[그림1]. 관리 콘솔 홈페이지</b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/110d36e0-796e-460e-9219-fca191688345" width="720" height=""/>
<p><b>[그림2]. Putty</b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/60af4567-abf3-4bc6-9461-0b0bad1e25d2" width="720" height=""/>
<p><b>[그림3]. Console Window</b></p>
</center>

# 3. 서버
## 3.1. SpringBoot Project 생성
1. Java 환경확인

```java
java -version
```

```java
gradle --version
spring --version
```

2. Spring BootCLI의 기본 init 설정 확인

```java
spring init --list

:: Service capabilities ::  https://start.spring.io

Supported dependencies
…

Parameters
+-------------+------------------------------------------+------------------------------+
| Id          | Description                              | Default value                |
+-------------+------------------------------------------+------------------------------+
| artifactId  | project coordinates (infer archive name) | demo                         |
| bootVersion | spring boot version                      | 2.5.6                        |
| description | project description                      | Demo project for Spring Boot |
| groupId     | project coordinates                      | com.example                  |
| javaVersion | language level                           | 11                           |
| language    | programming language                     | java                         |
| name        | project name (infer application name)    | demo                         |
| packageName | root package                             | com.example.demo             |
| packaging   | project packaging                        | jar                          |
| type        | project type                             | maven-project                |
| version     | project version                          | 0.0.1-SNAPSHOT               |
+-------------+------------------------------------------+------------------------------+
```

3. 사용하고자 하는 의존성 추가후 프로젝트 생성

```java
# Gradle 프로젝트 생성
spring init --dependencies={의존성 패키지} --java-version=17 --type=gradle-project {프로젝트 이름}
```

4. 생성된 프로젝트 확인

- build.gradle
```java
id 'org.springframework.boot' version '2.5.5'
```
boot version을 위와 같이 맞춰주셔야 에러가 안납니다. 

```java
[guser@java WatchDogs]$ ls -al

total 32
drwxr-xr-x 6 guser users  169  5월 27 00:48 .
drwxr-xr-x 3 guser users   23  5월 26 22:09 ..
-rw-r--r-- 1 guser users  444  5월 26 22:09 .gitignore
drwxr-xr-x 6 guser users   73  5월 26 22:21 .gradle
-rw-r--r-- 1 guser users 1288  5월 26 22:09 HELP.md
drwxr-xr-x 6 guser users   94  5월 27 00:39 build
-rw-r--r-- 1 guser users  904  5월 27 00:48 build.gradle
drwxr-xr-x 3 guser users   21  5월 26 22:09 gradle
-rwxr-xr-x 1 guser users 8692  5월 26 22:09 gradlew
-rw-r--r-- 1 guser users 2918  5월 26 22:09 gradlew.bat
-rw-r--r-- 1 guser users   31  5월 26 22:43 settings.gradle
drwxr-xr-x 4 guser users   30  5월 26 22:09 src
```

### 3.2. SpringBoot(Gradle) Web Project 실행

```java

package com.example.Watchdogs.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

        @GetMapping("/sensorData")
        public String index() {
                return "Hello World! Hello Spring Boot!";
        }

}
```

gradlew 파일 있는 곳에서

- 웹 애플리케이션을 포그라운드 형태로 실행시킵니다.
```java
gradle bootRun
```

- 웹 애플리케이션을 백그라운드 형태로 실행시킵니다.
```java
nohup gradle bootRun > gradle.log 2>&1 &

pkill java(종료 명령어)
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/b6f81a00-3e1b-4af1-b80b-ff4a03e99358" width="720" height=""/>
<p><b>[그림4]. MYSQL 서버 접속 하기</b></p>
</center>

### 3.3. DB 서버 접속 및 연결

- application.properties
```properties
spring.application.name=WatchDogs
spring.datasource.url=jdbc:mysql://{db도메인주소}:3306/{db_name}
spring.datasource.username={user_name}
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.database-platform=org.hibernate.dialect.MySQLDialect
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.show_sql=true
spring.jpa.properties.hibernate.format_sql=true
```

```mysql
mysql -h {db도메인주소} -u {db사용자} -p
```


<center>
<img src="https://github.com/cisco/openh264/assets/56510688/9e546a4b-c21b-44df-80da-d86772afb634" width="720" height=""/>
<p><b>[그림5]. MYSQL 서버 접속 하기</b></p>
</center>