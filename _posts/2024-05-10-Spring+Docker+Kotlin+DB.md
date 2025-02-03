---
title: "Spring(MySQL) + Docker + Kotlin"
date: 2024-04-25 02:23:00 +0900
categories: [Sort]
tags: [CodingTest, Algorithm, Sort]
---
# 1. 프로젝트를 위한 실습
 프로젝트에서는 Server가 HardWare로부터 raw-data를 수신 받아 이상치를 검증하고 DB에 저장하는 시나리오 1과 Server가 DB로부터 정상적인 데이터를 가져와 Front에 Broadcast하는 시나리오 2로 간단하게 이루어져있다. 중간에 Admin은 Server에 송신하는 HardWare의 상태를 점검할 수 있도록 만들 것이다. 왜냐면 만약에 고객이 컴플레인이 들어오면 해당 장치에 무슨 이상이 있는지 단계별로 알아야하기 때문이다.

# 2.1. Host(Server) <- Docker(Client)
아직 하드웨어가 도착을 안해서 Docker에서 C 프로그램으로 Server에 Json 데이터를 보내는 Client 프로그램을 작성해서 하드웨어에서 서버로 보내는 신호를 대체 하였습니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/b626c744-3cd1-432e-8f53-9175ba789572" width="720" height=""/>
<p><b>[그림1]. 현재 실습 아키텍쳐</b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/4f2eab8a-c075-4db7-8e75-9910ac60937d" width="480" height=""/>
<p><b>[그림2]. Server 패키지 구조</b></p>
</center>

# 2.2. Client 소스 코드

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <curl/curl.h>
#include <sys/wait.h>
#include <time.h> // time 라이브러리 추가

// JSON 데이터를 서버로 보내는 함수
void sendSensorData(const char *json_data) {
    CURL *curl;
    CURLcode res;

    // libcurl 초기화
    curl = curl_easy_init();
    if (curl) {
        // 요청을 보낼 서버 URL
        curl_easy_setopt(curl, CURLOPT_URL, "http://host.docker.internal:8080/sensorData");

        // POST 메서드 사용
        curl_easy_setopt(curl, CURLOPT_POST, 1L);

        // 전송할 데이터 설정
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, json_data);

        // Content-Type 설정 (JSON 형식)
        struct curl_slist *headers = NULL;
        headers = curl_slist_append(headers, "Content-Type: application/json");
        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);

        // 요청 보내기
        res = curl_easy_perform(curl);

        // 에러 처리
        if (res != CURLE_OK)
            fprintf(stderr, "curl_easy_perform() failed: %s\n", curl_easy_strerror(res));

        // libcurl 세션 종료
        curl_easy_cleanup(curl);

        // 헤더 리스트 정리
        curl_slist_free_all(headers);
    }
}

int main(void)
{
    int pipefd[2];
    pid_t pid;

    // 파이프 생성
    if (pipe(pipefd) == -1) {
        perror("pipe");
        exit(EXIT_FAILURE);
    }

    // 자식 프로세스 생성
    pid = fork();

    if (pid == -1) {
        perror("fork");
        exit(EXIT_FAILURE);
    } else if (pid == 0) { // 자식 프로세스
        close(pipefd[1]); // 자식 프로세스는 쓰기 파이프를 닫음

        // 파이프로부터 데이터 읽기 및 HTTP 요청 보내기
        char buffer[256];
        ssize_t bytes_read;
        while ((bytes_read = read(pipefd[0], buffer, sizeof(buffer))) > 0) {
            // JSON 형식으로 변환
            char json_data[512]; // 더 큰 크기로 수정
            // 현재 시간 구하기
            time_t rawtime;
            struct tm *timeinfo;
            time(&rawtime);
            timeinfo = localtime(&rawtime);
            // 날짜와 시간을 형식에 맞게 포맷팅
            char datetime[20];
            strftime(datetime, sizeof(datetime), "%Y-%m-%d %H:%M:%S", timeinfo);
            sprintf(json_data, "{\n\"sensorData\": \"%s\",\n\"date\": \"%s\",\n\"sensorType\": \"sensor_type\",\n\"sensorId\": \"sensor_id\",\n\"port\": 8080\n}", buffer, datetime);

            // libcurl을 사용하여 HTTP POST 요청 보내는 함수 호출
            sendSensorData(json_data);

            printf("Sending data: %s\n", json_data);
            sleep(1); // 1초 대기
        }

        close(pipefd[0]); // 파이프 닫기
        _exit(EXIT_SUCCESS);
    } else { // 부모 프로세스
        close(pipefd[0]); // 부모 프로세스는 읽기 파이프를 닫음

        // 부모 프로세스는 사용자가 Ctrl+C를 누를 때까지 계속해서 데이터를 전송
        while (1) {
            // 센서 데이터 생성
            char sensor_data[256];
            sprintf(sensor_data, "%ld", random() % 1000);

            // 파이프로 데이터 전송
            write(pipefd[1], sensor_data, sizeof(sensor_data));

            // 0.5초 대기
            usleep(500000); // 0.5초 대기
        }

        close(pipefd[1]); // 파이프 닫기
        wait(NULL); // 자식 프로세스가 종료되길 기다림
    }

    return 0;
}
```

### Client가 보내는 Json 형식

```json
{
    "SensorData": 1~1000 Random Value,
    "date" : YYYY%MM%DD-HH:MM:SS, 
    "sensorType" : TYPE, 
    "sensorId" : ID,
    "PORT": 8080
}
```





# 3.1. Server 프로그램

### Controller(sendToDB)

```java
package com.WatchDogs.controller;

import com.WatchDogs.services.sensorDataService;
import com.WatchDogs.entities.SensorDataForDB;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/sensorDataStore")
public class sendToDB {

    @Autowired
    private sensorDataService sensorDataService;
    
    @PostMapping
    public SensorDataForDB createSensorData(@RequestBody SensorDataForDB sensorData) {
        String tmp = "Received sensor data: " + sensorData.getSensorData() +
                ", date: " + sensorData.getDate() +
                ", sensor type: " + sensorData.getSensorType() +
                ", sensor ID: " + sensorData.getSensorId() +
                ", PORT: " + sensorData.getPort();
        System.out.println(tmp);
        return sensorDataService.saveSensorData(sensorData);
    }

}
```

### entity(SensorDataForDB)

```java
package com.WatchDogs.entities;

import jakarta.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "SensorData")
public class SensorDataForDB {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "sensor_data")
    private String sensorData;

    @Column(name = "date")
    private String date;

    @Column(name = "sensor_type")
    private String sensorType;

    @Column(name = "sensor_id")
    private String sensorId;

    @Column(name = "port")
    private int port;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getSensorData() {
        return sensorData;
    }

    public void setSensorData(String sensorData) {
        this.sensorData = sensorData;
    }

    public String getDate() {
        return date;
    }

    public void setDate(String date) {
        this.date = date;
    }

    public String getSensorType() {
        return sensorType;
    }

    public void setSensorType(String sensorType) {
        this.sensorType = sensorType;
    }

    public String getSensorId() {
        return sensorId;
    }

    public void setSensorId(String sensorId) {
        this.sensorId = sensorId;
    }

    public int getPort() {
        return port;
    }

    public void setPort(int port) {
        this.port = port;
    }
}
```

### Repository(SensorDataRepository)

```java
package com.WatchDogs.repository;


import com.WatchDogs.entities.SensorDataForDB;
import org.springframework.stereotype.Repository;
import org.springframework.data.jpa.repository.JpaRepository;

@Repository
public interface SensorDataRepository extends JpaRepository<SensorDataForDB, Long> {
}
```

### Service(sensorDataService)

```java
package com.WatchDogs.services;

import com.WatchDogs.entities.SensorDataForDB;
import com.WatchDogs.repository.SensorDataRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class sensorDataService {
    @Autowired
    private SensorDataRepository sensorDataRepository;

    public SensorDataForDB saveSensorData(SensorDataForDB sensorData) {
        return sensorDataRepository.save(sensorData);
    }
}

```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/fe5c5d31-e615-474b-9807-e92f41aa6910" width="720" height=""/>
<p><b>[그림3]. Client(sendToServer) </b></p>
</center>


## 3.2. MySQL 연결
Spring에서 Docker와 연결하여 Mysql을 Repository로 옮겨서 실행할 수 있는 세팅이 있길래 해당 세팅으로 진행하였습니다.
### compose.yml

1. spring user 만들기

```sql
create user 'springuser'@'%' identified by 'ThePassword'; -- Creates the user

grant all on sensordata.* to 'springuser'@'%'; -- Gives all privileges to the new user on the newly 
created database
```

2. database 생성

```sql
CREATE DATABASE SensorData;

USE SensorData;

CREATE TABLE SensorData (
    id INT AUTO_INCREMENT PRIMARY KEY,
    sensor_data INT,
    date DATETIME,
    sensor_type VARCHAR(50),
    sensor_id VARCHAR(50),
    port INT
);
```

3. compose.yml 설정

```
services:
  mysql:
    image: 'mysql:latest'
    environment:
      - 'MYSQL_DATABASE=sensordata'
      - 'MYSQL_PASSWORD=verysecret'
      - 'MYSQL_ROOT_PASSWORD=verysecret'
      - 'MYSQL_USER=springuser'
    ports:
      - '3306'

```

* docker에 설치된 mysql에 접속할땐 아래 명령어를 사용합니다.
```CLI
docker exec -it watchdogs-mysql-1 mysql -u springuser -p
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/1afc9bab-b1bf-466a-800d-9d630a6e9fe1" width="720" height=""/>
<p><b>[그림4]. sensorData DB</b></p>
</center>

* cmd상에서 mysql 명령어 못쓸 때 방법 시스템 환경변수 "Path"에 해당 경로 추가 C:\Program Files\MySQL\MySQL Server 8.4\bin 

# 4. 네트워크 구조

## 4.1. Docker와 HOST간의 네트워크

- <code>eth0</code> : 호스트가 외부와 연결할 떄 사용하는 IP가 할당된 호스트 네트워크 인터페이스
- <code>docker0</code> : Docker default bridge Component입니다. eth0과 container와 네트워크를 연결할 수 있습니다.
- <code>veth</code> : 컨테이너 내부IP를 외부와 연결해주는 가상 인터페이스 입니다. Container가 생성될 때 동시에 생성되며 eth0와 연결 시켜 Container가 외부와 통신이 가능해집니다.

아래를 살펴보면 <code>docker network ls</code> 명령어를 통해 Docker에 설치되어있는 네트워크 구조를 살펴볼 수 있습니다.
<code>bridge</code>, <code>host</code>, <code>none</code>은 기본적으로 도커 자체에서 제공하는 네트워크 구조입니다.

<code>redis</code>는 docker image로 Redis DB설치 했을때 설치된것 같습니다.

<code>watchdogs_default</code>는 Spring의 watchdogs 프로젝트의 mysql bridge입니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/571d254a-30ab-4fcc-8765-fd7a2c124411" width="720" height=""/>
<p><b>[그림5]. docker network</b></p>
</center>


<center>
<img src="https://github.com/cisco/openh264/assets/56510688/25514ca7-0a10-4fcc-a4c3-ea715ba64741" width="720" height=""/>
<p><b>[그림6]. 현재 실습 데이터 흐름</b></p>
</center>