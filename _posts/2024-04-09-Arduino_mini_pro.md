---
title: "아두이노 및 센서 핀아웃 공부"
date: 2024-03-28 02:23:00 +0900
categories: [Arduino]
tags: [TeamProject, Arduino, Sensor, GYRO]
---
# 1. 소개글
2024년 맞이해서 창업동아리 활동을 시작하게 되었습니다. 첫번째 프로젝트로 애완견을 위한 스마트 커넥티드 웨어러블을 제작하기로 하여서 아두이노로 기기를 제작해보기로 하였습니다. 

```
사용 센서 및 MCU
1. Arduino pro mini
2. mpu6050
3. ESP8266-6
4. 3.3V <-> 5V 변압기(logic level converter)
5. 100mm 구부림 휨 압력센서 500G
6. Pulse Sensor Heart Rate Sensor
7. NEO-6M GY-GPS6MV2
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/8b399d39-8b84-4697-9a11-57ea6be4abc9" width="" height=""/>
<p><b>[그림1]. 사용 센서(mm)</b></p>
</center>


# 2. HardWare Specification
## 2.1. Arduino pro mini
작은 케이스안에 모든 부품을 조립해야하기 때문에 범용성 높은 Arduino pro mini를 선택했습니다. 
보통 아두이노 초기화 시 Flash에서 데이터를 읽어 SRAM에 저장하여 코드 실행 시 빠르게 처리 할 수 있도록 작동됩니다.

| 아두이노 보드종류 |   미니   |
|:----------------:|:------:|
| MCU              | ATmega328 |
| 동작 주파수(HZ)  | 8 or 16 |
| 크기(mm)         | 17.8x33 |
| 동작 전압(V)     | 3.3 or 5 |
| 추천 입력 전압(V)     | 3.35 ~ 12(3.3V model) or 5 ~ 12(5V model) |
| 플래쉬(KByte)    | 32      |
| EEPROM(KByte)    | 2       |
| SRAM(KByte)      | 1       |
| 디지털(핀)       | 14      |
| PWM(핀)          | 6       |
| 아날로그(핀)     | 6       |
| 전류(mA)             | 40      |
| 케이블           | x       |

- 플래쉬 : 비휘발성 저장장치
- EEPROM : 보통 부트로더 저장장치
- SRAM : 동적 메모리 부족할 때 땡겨 쓸 수 있음.
- PWM : Pulse Width Modulation의 약자로 아날로그와 유사한 제어가 가능한 디지털 신호를 입출력 할 수 있다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/29a63d1e-dc60-42f1-8989-cc6802f16e3b" width="720" height=""/>
<p><b>[그림2]. ARDUINO PRO MINI</b></p>
</center>

## 2.2. mpu6050
강아지의 앞발 움직임을 통해 현재 무슨 동작을 취하고 있는지 인식하기 위해서 앞발의 3차원 움직임을 추적할 자이로센서를 선택했습니다.

해당 센서는 전원 연결 후 전원을 관리하는 Register값을 수정해야합니다. Register 당 1Byte로 데이터 통신합니다.



<center>
<img src="https://github.com/cisco/openh264/assets/56510688/1dd549e8-e017-4fc3-9f92-462a81c5faae" width="720" height=""/>
<p><b>[그림2]. MPU6050</b></p>
</center>



```Arduino
#include <Wire.h>

void setup() {
    // Initialize the I2C communication
    Wire.begin();
    
    // Begin transmission to the device with address 0x68
    Wire.beginTransmission(0x68);
    
    // Specify the register address (107) to write to
    Wire.write(107);
    
    // Write the value 0 to the specified register
    Wire.write(0);
    
    // End the transmission
    Wire.endTransmission();
}

void loop() {
    // Your loop code goes here
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/62a33cdb-067e-4bed-9069-9a02c532b10f" width="720" height=""/>
<p><b>[그림3]. MPU6000-Register-Map 107</b></p>
</center>

MPU6050 센서는 2G, 4G, 8G, 16G의 중력가속도를 검출 할 수 있습니다. 추가로 각가속도는 250deg/sec ~ 2000deg/sec로 시간당 측정되는 최대 각도값입니다.

- 각가속도 센서 : [250 deg/sec -> (Raw-Data)/131] ~ [2000 deg/sec ->(Raw-Data)/16.4]의 데이터를 보여줍니다. IC2 통신으로 받은 Raw-Data 값을 설정된 환경에 따라 {131, 65.5, 32.8, 16.4}로 나누면 각가속도 값을 측정하여 얻을 수 있습니다.

- 중력가속도 센서 : [2G -> (Raw-Data)/16384] ~ [16G -> (Raw-Data)/2048]의 데이터를 보여줍니다. 설정된 환경 값에 따라 단위 가속도 G의 Raw-Data값을 보여주는 것입니다. 데이터를 {16384, 8192, 4096, 2048}로 나눠주면 해당값이 1G입니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/67ba48f2-2e01-4929-97fb-0f2ea055f6f0" width="720" height=""/>
<p><b>[그림4]. GYROSCOPE Spec</b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/98a41c39-2a29-4978-a5c0-68e063e4579a" width="720" height=""/>
<p><b>[그림5]. Accelerometer Spec</b></p>
</center>

센서 감도에 따른 단위 설정값을 알았으니 Register-27를 참고해 어떤 설정값을 사용해야하는지 살펴봅니다. 이번 테스트에서는 가장 작은 감도의 [250 deg/sec], [2G]로 설정할겁니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/9f368d83-1a1b-4202-97d0-d3c578b7502d" width="720" height=""/>
<p><b>[그림6]. MPU6000-Register-Map 27</b></p>
</center>


<center>
<img src="https://github.com/cisco/openh264/assets/56510688/bb45c76b-82f4-4824-9ea0-fdbe85bf870b" width="720" height=""/>
<p><b>[그림7]. MPU6000-Register-Map 28</b></p>
</center>

```Arduino
#include <Wire.h>

void setup() {
    Wire.begin();
    Wire.beginTransmission(0x68);
    Wire.write(27);
    Wire.wrtie(0);
    Wire.endTransmission();
    Wire.beginTransmission(0x68);
    Wire.write(28);
    Wire.write
}
```

59번 ~ 64번 레지스터의 값으로부터 X, Y, Z축에 감지된 가속도 값을 얻을 수 있습니다. [15:8],[7:0]은 비트 자리수를 의미 합니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/20fab824-293d-47d3-91a3-60d8f01ac502" width="720" height=""/>
<p><b>[그림8]. MPU6000-Register-Map 59 to 64</b></p>
</center>

67번 ~ 72번 레지스터의 값으로부터 X, Y, Z축의 각가속도 값을 얻을 수 있습니다.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/a1c6be20-47fb-4212-b5a1-82df4b841d6d" width="720" height=""/>
<p><b>[그림9]. MPU6000-Register-Map 67 to 72</b></p>
</center>

## 2.3. ESP8266-6(Wifi Chip)

```Arduino
#include <ESP8266_HardSer.h>
#include <BlynkSimpleShieldEsp8266_HardSer.h>

#define EspSerial Serial


ESP8266 wifi(EspSerial);

// You should get Auth Token in the Blynk App.
// Go to the Project Settings (nut icon).
char auth[] = "your_Token";



void setup()
{
 
  Serial.begin(9600);
  delay(10);
  EspSerial.begin(9600);
  delay(10);

  Blynk.begin(auth, wifi, "ssid", "pass");
  delay(10); 

  while (Blynk.connect() == false) {
  }

}

void loop()
{
Blynk.run();
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/5a2bdcbd-0dd9-4575-90e6-d5ee8f3a4646" width="720" height=""/>
<p><b>[그림10]. ESP8266-6 Pinout</b></p>
</center>




<center>
<img src="https://github.com/cisco/openh264/assets/56510688/97c5eb59-41e1-4184-b00b-38b6e815283b" width="720" height=""/>
<p><b>[그림11]. ESP8266-6 Circuit</b></p>
</center>

## 2.4. 100mm 구부림 휨 압력센서 500G

|||
|------|------|
|길이|100mm|
|최대 하중|10kg|
|저항|100MΩ|
|동작 전압|DC 3.3V|
|응답 시간|<1ms|
|복구 시간|<15ms|
|동작 온도|-20도씨 ~ 60도씨|

```Arduino
int FlexPin = A0; // 센서값을 읽기 위해 아날로그핀 0번을 FlexPin에 지정한다.

void setup()
{
    Serial.begin(9600); // 센서값을 읽기 위해 시리얼 모니터를 사용할 것을 설정.
}

void loop()
{
    int FlexVal; // 센서값을 저장할 변수
    FlexVal = analogRead(FlexPin); // 아날로그를 입력 받음 (0~1023)

    Serial.print("sensor: "); // sensor: 라는 텍스트를 프린트한다.
    Serial.println(FlexVal); // println은 줄바꿈 명령이다. flexVal의 값을 출력한다.

    delay(500);
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/de867130-99ff-45f7-b411-8d07d3edce0c" width="720" height=""/>
<p><b>[그림12]. 플렉시블 압력센서 회로도</b></p>
</center>

## 2.5. Pulse Sensor Heart Rate Sensor

- DC 3.3V ~ 5V에서 작동합니다.
- <4mA이상 사용합니다. 


```arduino
int sensorPin= A0;
int startTime=0;
int count=0;
int count1=1;
int beat=0;
unsigned int timeS=0;
int hearTbeat=0;

void setup() 
{
    // put your setup code here, to run once:
    Serial.begin(9600);
    pinMode(2,OUTPUT);
    pinMode(sensorPin,INPUT);
}

void loop() 
{
    int sensorData=analogRead(sensorPin);
    if(sensorData>512)
    {
        count=count+1;
        beat=beat+1;
        delay(100);
        if(count1==1)
    { 
    startTime=millis();
    count1=count1+1;
    }

    }
    timeS= millis()-startTime;
    if((timeS>9500)&(timeS<11500))
    { 
        hearTbeat=count*6;
        delay(5000);
        startTime=0;
        count=0;
        timeS=0;
        beat=0;
        count1=1;
    }
    Serial.print("Heart Beat:");
    Serial.println(hearTbeat);
}
```

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/a8c1be50-f8cb-49a0-b201-f13c311e5cb1" width="720" height=""/>
<p><b>[그림13]. 심장 박동 센서 Pin-out</b></p>
</center>

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/60682ab5-dbd5-4b2c-ad3f-ebb7f3f4a5be" width="720" height=""/>
<p><b>[그림14]. 심장 박동 센서 회로도</b></p>
</center>

## 2.6. NEO-6M GY-GPS6MV2

- GND is the ground pin and needs to be connected to the GND pin on the Arduino.

- TxD (Transmitter) pin is used for serial communication.

- RxD (Receiver) pin is used for serial communication.

- VCC supplies power to the module. You can connect it directly to the 5V pin on the Arduino.

<center>
<img src="https://github.com/cisco/openh264/assets/56510688/15cd83a2-790f-49f3-8936-dc285c5e3dda" width="720" height=""/>
<p><b>[그림15]. GPS Pin-out</b></p>
</center>


<center>
<img src="https://github.com/cisco/openh264/assets/56510688/929d0ea1-8166-4b1b-855c-a7dbeab785a1" width="720" height=""/>
<p><b>[그림16]. GPS 회로도</b></p>
</center>

아래 소스코드는 GPS 모듈로부터 직접적으로 데이터를 받아오는 코드입니다.

```arduino
#include <SoftwareSerial.h>
#include <TinyGPS.h>

SoftwareSerial mySerial(3, 4); // RX, TX
TinyGPS gps;

void setup()  {
  mySerial.begin(9600);
}

void loop() {
  bool ready = false;
  if (mySerial.available()) {
    char c = mySerial.read();
    if (gps.encode(c)) {
      ready = true;
    }
  }

  // Use actual data
  if (ready) {
    // Use `gps` object
  }
}
```

아래 소스코드는 아두이노를 Bridge로 사용하여 GPS데이터를 다른 기기로 송신하는 코드입니다. node.js를 이용해 해당 데이터를 해석할 수 있습니다.

```arduino
#include <SoftwareSerial.h>

SoftwareSerial mySerial(3, 4); // RX, TX

void setup()  {
  Serial.begin(9600);
  mySerial.begin(9600);
}

void loop() {
  bool ready = false;
  if (mySerial.available()) {
    char c = mySerial.read();
    Serial.write(c);
  }
}
```

```js
var file = '/dev/tty.usbmodem1411'; // Your Arduino serial device

var GPS = require('gps');
var SerialPort = require('serialport');
var port = new SerialPort.SerialPort(file, {
  baudrate: 9600,
  parser: SerialPort.parsers.readline('\r\n')
});

var gps = new GPS;

gps.on('data', function(data) {
  console.log(data);
});

port.on('data', function(data) {
  gps.update(data);
});
```