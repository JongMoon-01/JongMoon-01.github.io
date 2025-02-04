---
title: "Dashboard홈페이지"
date: 2024-07-09 02:23:00 +0900
categories: [Spring]
tags: [Java, Spring, Back-end, Server, html]
---
# 1. 소개

# 2. Html
## 2.1. thymeleaf View

thymeleaf는 View Template 종류 중 하나입니다. html 태그를 기반으로하여 th:attribute를 이용해 동적 View를 제공합니다.

* View Template : 컨트롤러가 전달하는 데이터(보통 Model)을 이용하여 동적으로 화면을 구성할 수 있게 해줍니다.
### 2.1.1. 종속성

Gradle에서 작성하였으므로 Build.gradle 기준으로 아래 종속성을 추가해주어야합니다.

```gradle
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

### 2.1.2. 기본 사용법

1. 변수식 <code>${}</code> 예시

자바 코드

```java
import java.util.HashMap;
import java.util.Map;

public class ThymeleafExample {
    public Map<String, Object> getModel() {
        Map<String, Object> model = new HashMap<>(); // HashMap Type model변수를 선언한다.
        model.put("username", "JohnDoe"); //Username이라는 객체에 JohnDoe라는 Value를 담는다.
        return model;
    }
}
```

HTML 코드

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf Example</title>
</head>
<body>
    <p th:text="${username}">Default Username</p> <!-- ${username}은 Java에서 선언된 username을 참조합니다.-->
</body>
</html>

```

2. 메세지방식 <code>#{}</code> 예시

자바 코드

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.support.ResourceBundleMessageSource;

public class ThymeleafMessageExample {
    @Bean // 빈 객체에 메세지번들 객체를 담아 dispatchServlet에 등록한다.
    public ResourceBundleMessageSource messageSource() {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        messageSource.setBasename("messages"); // messages파일을 기본 이름으로 정한다.
        return messageSource;
    }
}

```

messages.properties

```css
welcome.message=Welcome to Thymeleaf, {0}! <!-- Resource폴더 아래에서 생성된 message.properties 파일을 참조한다. 0에는 sh에 쓰이는 형식처럼 변수가 들어갈 자리를 나타낸다.-->
```

HTML 코드

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf Message Example</title>
</head>
<body>
    <p th:text="#{welcome.message(${username})}">Welcome message</p> <!--messages.properties에 저장된 welcome.message를 출력한다. {0}에는 ${username}이 들어가게 된다.-->
</body>
</html>

```

3. 객체변수식 <code>*{}</code> : 

자바 코드

```java
import java.util.HashMap;
import java.util.Map;

public class ThymeleafObjectVariableExample {
    public User getUser() {
        return new User("JohnDoe"); // User객체를 "JohnDoe"값으로 초기화 한후 생성해서 return한다.
    }

    public class User {
        private String username;
        public User(String username) {
            this.username = username;
        }
        public String getUsername() {
            return username;
        }
    }
}

```

HTML 코드

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf Object Variable Example</title>
</head>
<body>
    <div th:object="${user}"> <!--아래 username변수는 User속성을 참조하여 변수값을 출력합니다.-->
        <p th:text="*{username}">Default Username</p>
    </div>
</body>
</html>

```

4. 링크방식 <code>@{}</code> : 

자바 코드

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/example")
public class ThymeleafLinkExampleController {
    @GetMapping //GET 요청으로 /example에 접근하면 example html을 반환한다.
    public String example() {
        return "example";
    }
}

```

HTML 코드

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf Link Example</title>
</head>
<body>
    <a th:href="@{/example}">Go to Example Page</a> <!--th:href="@{/example}"는 "/example" 경로로 링크를 생성합니다.-->
</body>
</html>

```

## 2.2. Chart js

html canvas tag를 사용하여 다양한 종류의 차트를 생성할 수 있는 데이터 시각화 라이브러리이다.

* 참고 사이트 : https://www.chartjs.org/docs/latest/getting-started/

### 2.2.1. 다운로드 방법
```
//cdn
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

//npm
npm install chart.js
```

### 2.2.2. JS map함수

어떤 배열에 있는 모든 요소들의 값을 변경해서 새로운 배열을 만들어야하는 상황에 적합합니다. 이때 for문을 사용해서 구현할 수 도 있지만 map함수를 사용하면 더 간편하게 가능합니다.

예를 들면 아래와 같은 배열이 있다 가정했을때

```html
let arr = [3,4,5,6];
```

첫번째로 for문을 사용하여 모든 요소에 3을 곱한다고 가정하면 아래의 방법으로 구현이 가능합니다. 결과는 arr가 모든요소에 3이 곱해진 배열로 바뀝니다.

```html
let arr = [3,4,5,6];

for (let i = 0; i < arr.length; i++) {
    arr[i] = arr[i] * 3;
}

console.log(arr);
```

두번째로 map함수를 사용하면 아래와 같이 구현할 수 있습니다. 결과는 arr와 3을 곱해진 modifiedArr가 생성됩니다.

```html
let arr = [3,4,5,6];

let modifiedArr = arr.map(function(element){
    return element*3;
});

console.log(modifiedArr); // [9,12,15,18]
```

JsonData에도 동일하게 적용이 가능합니다. 아래와 같은 데이터가 있다고 가정합니다.

```html
let users = [
    {firstname : "Susan", lastname : "stewar"},
    {firstname : "Daniel", lastname : "Longbottom"},
    {firstname : "Jacob", lastname : "Black"}
]
```

map 함수를 이용하여 배열을 순환하며 새 배열로 구성할 수 있습니다. 결과는 firstname과 lastname이 모두 합해진 새로운 List Type <code>userFullnames</code>변수가 생성됩니다.

```html
let users = [...];

let userFullnames = users.map(function(element){
    return `${element.firstname} ${element,lastname}`;
})

console.log(userFullnames); // ["Susan stewar", "Daniel LongBottom", "Jacob Black"]
```


## 2.3. Source Code

1. <code>/*<![CDATA[*/와 /*]]>*/</code>
    - XML또는 XHTML 문서에서 CDATA Section임을 명시적을로 표시합니다.
    - CDATA는 Character Data의 약어로 텍스트 데이터안에 특수문자가 포함되도 이를 해석하지 않도록 방지합니다.
2. <code>const mpu6050Data = /*[[${mpu6050Data}]]*/ [];</code>
    - Thymeleaf 구문으로 JavaScript변수를 Thymeleaf변수('mpu6050Data')로 치환하는 역할을 합니다.
    - const mpu6050Data = /*[[${mpu6050Data}]]*/ [];는 실제로 서버에서 렌더링될 때 /*[[${mpu6050Data}]]*/ 부분이 서버 데이터로 치환됩니다. 예를 들어, 서버에서 mpu6050Data가 [1, 2, 3]이라면, 치환 후에는 const mpu6050Data = [1, 2, 3];가 됩니다.
    - 서버에서 mpu6050Data가 전달되지 않으면 기본값으로 빈 배열 []이 사용됩니다.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Dashboard</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body>
<h1>Dashboard</h1>
<div>
    <canvas id="mpu6050Chart" width="400" height="200"></canvas>
    <canvas id="flex3v3Chart" width="400" height="200"></canvas>
</div>
<script th:inline="javascript">
    /*<![CDATA[*/
    const mpu6050Data = /*[[${mpu6050Data}]]*/ [];
    const flex3v3Data = /*[[${flex3v3Data}]]*/ [];
    console.log(mpu6050Data);
    console.log(flex3v3Data);
    /*]]>*/
</script>
<script>
    document.addEventListener("DOMContentLoaded", function() {
        const mpu6050ChartCtx = document.getElementById('mpu6050Chart').getContext('2d'); <!--mpu6050Chart Canvas에 mpu6050ChartCtx변수에 담긴 그래픽 텍스트를 getContext메소드를 이용해서 가져와 그립니다.-->
        const flex3v3ChartCtx = document.getElementById('flex3v3Chart').getContext('2d');

        new Chart(mpu6050ChartCtx, {
            type: 'line',
            data: {
                labels: mpu6050Data.map(data => data.id),
                datasets: [
                    {
                        label: 'MPU6050Gx',
                        data: mpu6050Data.map(data => data.mpu6050Gx),
                        borderColor: 'rgba(255, 99, 132, 1)',
                        fill: false
                    },
                    {
                        label: 'MPU6050Gy',
                        data: mpu6050Data.map(data => data.mpu6050Gy),
                        borderColor: 'rgba(54, 162, 235, 1)',
                        fill: false
                    },
                    {
                        label: 'MPU6050Gz',
                        data: mpu6050Data.map(data => data.mpu6050Gz),
                        borderColor: 'rgba(75, 192, 192, 1)',
                        fill: false
                    },
                    {
                        label: 'MPU6050Ax',
                        data: mpu6050Data.map(data => data.mpu6050Ax),
                        borderColor: 'rgba(153, 102, 255, 1)',
                        fill: false
                    },
                    {
                        label: 'MPU6050Ay',
                        data: mpu6050Data.map(data => data.mpu6050Ay),
                        borderColor: 'rgba(255, 159, 64, 1)',
                        fill: false
                    },
                    {
                        label: 'MPU6050Az',
                        data: mpu6050Data.map(data => data.mpu6050Az),
                        borderColor: 'rgba(255, 206, 86, 1)',
                        fill: false
                    }
                ]
            },
            options: {
                scales: {
                    x: {
                        type: 'linear',
                        position: 'bottom'
                    }
                }
            }
        });

        new Chart(flex3v3ChartCtx, {
            type: 'line',
            data: {
                labels: flex3v3Data.map(data => data.id),
                datasets: [
                    {
                        label: 'Flex3v3Val',
                        data: flex3v3Data.map(data => data.flex3v3Val),
                        borderColor: 'rgba(75, 192, 192, 1)',
                        fill: false
                    }
                ]
            },
            options: {
                scales: {
                    x: {
                        type: 'linear',
                        position: 'bottom'
                    }
                }
            }
        });
    });
</script>
</body>
</html>
```

CDATA 구문으로 Server로부터 받은 Data 일부분

```html
    /*<![CDATA[*/
    const mpu6050Data = [{"id":1,"mpu6050Ax":-920,"mpu6050Ay":-3764,"mpu6050Az":14764,"mpu6050Gx":-7,"mpu6050Gy":69,"mpu6050Gz":-172,"timestamp":"2024-07-14T00:42:17"}, ......생략 ,{"id":202,"mpu6050Ax":0,"mpu6050Ay":0,"mpu6050Az":0,"mpu6050Gx":0,"mpu6050Gy":0,"mpu6050Gz":0,"timestamp":"2024-07-15T19:48:29"}];
    const flex3v3Data = [{"id":1,"flex3v3Val":0,"timestamp":"2024-07-14T00:42:17"},.............생략,{"id":202,"flex3v3Val":0,"timestamp":"2024-07-15T19:48:29"}];
    console.log(mpu6050Data);
    console.log(flex3v3Data);
    /*]]>*/
```

<center>
<img src="https://github.com/user-attachments/assets/435997e7-72b3-49e9-82dd-634125df497d" width="720" height=""/>
<p><b>[그림1]. Dashboard.html </b></p>
</center>



# 3. Spring MVC

## 3.1. dto
DTO(Data Transfer Object, 데이터 전송객체)란 프로세스(작업) 간에 데이터를 전달하는 객체를 의미합니다. Controller와 API 계층에서 사용됩니다.

<center>
<img src="https://github.com/user-attachments/assets/deae49f2-1924-4621-b132-6e05a2791c82" width="720" height=""/>
<p><b>[그림2]. DTO </b></p>
</center>

### 3.1.1. Flex3v3ValDTO

```java
package com.example.WatchDogs.dto;

import java.time.LocalDateTime;

public class Flex3v3ValDTO {

    private Long id;
    private int flex3v3Val;
    private LocalDateTime timestamp;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public int getFlex3v3Val() {
        return flex3v3Val;
    }

    public void setFlex3v3Val(int flex3v3Val) {
        this.flex3v3Val = flex3v3Val;
    }

    public LocalDateTime getTimestamp() {
        return timestamp;
    }

    public void setTimestamp(LocalDateTime timestamp) {
        this.timestamp = timestamp;
    }
}

```

### 3.1.2. Mpu6050GADTO

```java
package com.example.WatchDogs.dto;

import java.time.LocalDateTime;

public class Mpu6050GADTO {

    private Long id;
    private int mpu6050Ax;
    private int mpu6050Ay;
    private int mpu6050Az;
    private int mpu6050Gx;
    private int mpu6050Gy;
    private int mpu6050Gz;
    private LocalDateTime timestamp;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public int getMpu6050Ax() {
        return mpu6050Ax;
    }

    public void setMpu6050Ax(int mpu6050Ax) {
        this.mpu6050Ax = mpu6050Ax;
    }

    public int getMpu6050Ay() {
        return mpu6050Ay;
    }

    public void setMpu6050Ay(int mpu6050Ay) {
        this.mpu6050Ay = mpu6050Ay;
    }

    public int getMpu6050Az() {
        return mpu6050Az;
    }

    public void setMpu6050Az(int mpu6050Az) {
        this.mpu6050Az = mpu6050Az;
    }

    public int getMpu6050Gx() {
        return mpu6050Gx;
    }

    public void setMpu6050Gx(int mpu6050Gx) {
        this.mpu6050Gx = mpu6050Gx;
    }

    public int getMpu6050Gy() {
        return mpu6050Gy;
    }

    public void setMpu6050Gy(int mpu6050Gy) {
        this.mpu6050Gy = mpu6050Gy;
    }

    public int getMpu6050Gz() {
        return mpu6050Gz;
    }

    public void setMpu6050Gz(int mpu6050Gz) {
        this.mpu6050Gz = mpu6050Gz;
    }

    public LocalDateTime getTimestamp() {
        return timestamp;
    }

    public void setTimestamp(LocalDateTime timestamp) {
        this.timestamp = timestamp;
    }
}

```

## 3.2. Controller
### 3.2.1. SensorDataController

```java
package com.example.WatchDogs.controllers;

import com.example.WatchDogs.dto.Flex3v3ValDTO;
import com.example.WatchDogs.dto.Mpu6050GADTO;
import com.example.WatchDogs.services.Mpu6050DataService;
import com.example.WatchDogs.services.Flex3v3ValDataService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/sensor-data")
public class SensorDataController {

    private final Flex3v3ValDataService flex3v3ValDataService;
    private final Mpu6050DataService mpu6050DataService;

    @Autowired
    public SensorDataController(Flex3v3ValDataService flex3v3ValDataService, Mpu6050DataService mpu6050DataService) {
        this.flex3v3ValDataService = flex3v3ValDataService;
        this.mpu6050DataService = mpu6050DataService;
    }

    @GetMapping("/flex3v3/device/{deviceID}")
    public List<Flex3v3ValDTO> getFlex3v3DataByDeviceID(@PathVariable String deviceID) {
        return flex3v3ValDataService.getSensorDataByDeviceID(deviceID);
    }

    @GetMapping("/mpu6050/device/{deviceID}")
    public List<Mpu6050GADTO> getMpu6050DataByDeviceID(@PathVariable String deviceID) {
        return mpu6050DataService.getMpu6050DataByDeviceID(deviceID);
    }
}

```

## 3.2.2. DashboardController

```java
package com.example.WatchDogs.controllers;

import com.example.WatchDogs.dto.Flex3v3ValDTO;
import com.example.WatchDogs.dto.Mpu6050GADTO;
import com.example.WatchDogs.services.Flex3v3ValDataService;
import com.example.WatchDogs.services.Mpu6050DataService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.List;

@Controller
public class DashboardController {

    private final Flex3v3ValDataService flex3v3ValDataService;
    private final Mpu6050DataService mpu6050DataService;

    @Autowired
    public DashboardController(Flex3v3ValDataService flex3v3ValDataService, Mpu6050DataService mpu6050DataService) {
        this.flex3v3ValDataService = flex3v3ValDataService;
        this.mpu6050DataService = mpu6050DataService;
    }

    @GetMapping("/dashboard")
    public String showDashboard(Model model) {
        //device ID가 WatchDogs98DA600B9FDE인 데이터만을 조회하여서 dashboard View에 Model을 넘겨주게 된다.
        List<Flex3v3ValDTO> flex3v3Data = flex3v3ValDataService.getSensorDataByDeviceID("WatchDogs98DA600B9FDE"); 
        List<Mpu6050GADTO> mpu6050Data = mpu6050DataService.getMpu6050DataByDeviceID("WatchDogs98DA600B9FDE");

        model.addAttribute("flex3v3Data", flex3v3Data);
        model.addAttribute("mpu6050Data", mpu6050Data);

        return "dashboard";
    }
}

```

## 3.3. Repository
### 3.3.1. SensorReadingRepository

```java
package com.example.WatchDogs.repository;

import com.example.WatchDogs.entities.sensorDataForDB;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface SensorReadingRepository extends JpaRepository<sensorDataForDB, Long> {
    List<sensorDataForDB> findByDeviceID(String deviceID); // SensorReadingRepository는 SensorDataForDB에서 형성된 데이터를 DeviceID로 조회하여 저장한다.
}

```

## 3.4. Services
### 3.4.1. Flex3v3ValDataService

```java
package com.example.WatchDogs.services;

import com.example.WatchDogs.dto.Flex3v3ValDTO;
import com.example.WatchDogs.entities.sensorDataForDB;
import com.example.WatchDogs.repository.SensorReadingRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class Flex3v3ValDataService {

    private final SensorReadingRepository sensorDataRepository;

    @Autowired
    public Flex3v3ValDataService(SensorReadingRepository sensorDataRepository) {
        this.sensorDataRepository = sensorDataRepository;
    }
    //파이썬에서 볼법만 형식
    public List<Flex3v3ValDTO> getSensorDataByDeviceID(String deviceID) {
        return sensorDataRepository.findByDeviceID(deviceID) // deviceID에 일치하는 데이터를 Repository에서 수집한다.
                .stream() // stream형태로 변환한다.
                .map(this::convertToDTO) // 변환된 형태를 convertToDTO객체로 넘겨준다.
                .collect(Collectors.toList()); // convertToDTO 객체에서 생성자로 인해 초기화된 값을 Colletcer를 통해 List Type으로 수집된다.
    }
    //convertToDTO 객체의 생성자 역할을 해준다. Flex3v3ValDTO는 Flex3v3Val 데이터값과 auto Increment값인 ID 그리고 Timestamp만을 필요로 한다.
    private Flex3v3ValDTO convertToDTO(sensorDataForDB sensorData) {
        Flex3v3ValDTO dto = new Flex3v3ValDTO();
        dto.setId(sensorData.getId());
        dto.setFlex3v3Val(sensorData.getFlex3v3Val());
        dto.setTimestamp(sensorData.getTimestamp());
        return dto;
    }
}

```

### 3.4.2. Mpu6050DataService

```java
package com.example.WatchDogs.services;

import com.example.WatchDogs.dto.Mpu6050GADTO;
import com.example.WatchDogs.entities.sensorDataForDB;
import com.example.WatchDogs.repository.SensorReadingRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class Mpu6050DataService {

    private final SensorReadingRepository sensorDataRepository;

    @Autowired
    public Mpu6050DataService(SensorReadingRepository sensorDataRepository) {
        this.sensorDataRepository = sensorDataRepository;
    }

    public List<Mpu6050GADTO> getMpu6050DataByDeviceID(String deviceID) {
        return sensorDataRepository.findByDeviceID(deviceID)
                .stream()
                .map(this::convertToMpu6050DTO)
                .collect(Collectors.toList());
    }

    private Mpu6050GADTO convertToMpu6050DTO(sensorDataForDB sensorData) {
        Mpu6050GADTO dto = new Mpu6050GADTO();
        dto.setId(sensorData.getId());
        dto.setMpu6050Ax(sensorData.getMpu6050Ax());
        dto.setMpu6050Ay(sensorData.getMpu6050Ay());
        dto.setMpu6050Az(sensorData.getMpu6050Az());
        dto.setMpu6050Gx(sensorData.getMpu6050Gx());
        dto.setMpu6050Gy(sensorData.getMpu6050Gy());
        dto.setMpu6050Gz(sensorData.getMpu6050Gz());
        dto.setTimestamp(sensorData.getTimestamp());
        return dto;
    }
}

```

<center>
<img src="https://github.com/user-attachments/assets/10f817fd-21f2-4b4a-97da-4fc7ff7891ab" width="720" height=""/>
<p><b>[그림3]. DeviceID로 DB조회하기(GET 사용) </b></p>
</center>

<center>
<img src="https://github.com/user-attachments/assets/37fa01ac-6c63-4380-8475-cc5e51c13e25" width="720" height=""/>
<p><b>[그림4]. DeviceID로 Dashboard 홈페이지 시각화하기(Chartjs사용) </b></p>
</center>