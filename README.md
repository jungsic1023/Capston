캡스톤 가상환경 홈트레이닝 시스템
===================================   
### 1. 아두이노 시리얼 연결
>#### 1-1 Arduino로 사용하는 센서
>#### 1-2 Arduino IDE에서 Uno보드에 센서들을 연결하여 Unity로 전송하는 코드
>#### 1-3 Unity에서 Arduino를 시리얼포트로 받는 방법
### 2. Vive Unity 연결
### 3. Unity 개발 


<hr/>   

## 1. 아두이노 시리얼 연결
### 1-1 Arduino로 사용하는 센서
1. 자이로 가속도 센서 -> 자전거의 방향제어(좌 우)
2. 홀 센서 -> 자전거의 브레이크 제어
3. 적외선 센서 -> 자전거의 속도제어
<hr/>  

### 1-2 Arduino IDE에서 Uno보드에 센서들을 연결하여 Unity로 전송하는 코드

```cs
#include "SoftwareSerial.h"
#include "Wire.h"
SoftwareSerial mySerial(7, 8); // RX, TX //블루투스 센서
const int MPU_addr=0x68;  // I2C address of the MPU-6050
int16_t AcX,AcY,AcZ,Tmp,GyX,GyY,GyZ; //가속도 자이로센서
const int hallPin = 4; //홀센서
int infrared  = 3; //적외선센서
// LED는 디지털 6번핀으로 설정합니다.
int led = 6;
String sensorReading;   
String a = "2";
void setup() {
  Wire.begin();
  Wire.beginTransmission(MPU_addr);
  Wire.write(0x6B);  // PWR_MGMT_1 register
  Wire.write(0);     // set to zero (wakes up the MPU-6050)
  Wire.endTransmission(true);
 pinMode(infrared, INPUT);
  // LED 핀을 OUTPUT으로 설정합니다.
  pinMode(led, OUTPUT);
  Serial.begin(9600);
  mySerial.begin(9600);
}
void loop(){
  sensorReading = digitalRead(hallPin); 
   int state = digitalRead(infrared);
  Wire.beginTransmission(MPU_addr);
  Wire.write(0x3B);  // starting with register 0x3B (ACCEL_XOUT_H)
  Wire.endTransmission(false);
  Wire.requestFrom(MPU_addr,14,true);  // request a total of 14 registers
  AcX=Wire.read()<<8|Wire.read();  // 0x3B (ACCEL_XOUT_H) & 0x3C (ACCEL_XOUT_L)    
  AcY=Wire.read()<<8|Wire.read();  // 0x3D (ACCEL_YOUT_H) & 0x3E (ACCEL_YOUT_L)
  AcZ=Wire.read()<<8|Wire.read();  // 0x3F (ACCEL_ZOUT_H) & 0x40 (ACCEL_ZOUT_L)
  //Tmp=Wire.read()<<8|Wire.read();  // 0x41 (TEMP_OUT_H) & 0x42 (TEMP_OUT_L)
  GyX=Wire.read()<<8|Wire.read();  // 0x43 (GYRO_XOUT_H) & 0x44 (GYRO_XOUT_L)
  GyY=Wire.read()<<8|Wire.read();  // 0x45 (GYRO_YOUT_H) & 0x46 (GYRO_YOUT_L)
  GyZ=Wire.read()<<8|Wire.read();  // 0x47 (GYRO_ZOUT_H) & 0x48 (GYRO_ZOUT_L)
if(state == 0){
    // LED를 켜지도록 합니다.
    digitalWrite(led, HIGH);
    // 경보 메세지를 시리얼 모니터에 출력합니다.
  }
  /// 측정된 센서값이 0 이외(감지되지 않음) 이면 아래 블록을 실행합니다.
  else{
    // LED를 꺼지도록 합니다.
    digitalWrite(led, LOW);
    // 안전 메세지를 시리얼 모니터에 출력합니다.
     }
  if (mySerial.available())
    Serial.write(mySerial.read());
  if (Serial.available())
    Serial.println(sensorReading+" "+AcY+" "+AcX+" "+AcZ+" "+GyX+" "+GyY+" "+GyZ+" "+state);
  mySerial.println(sensorReading+" "+AcY+" "+AcX+" "+AcZ+" "+GyX+" "+GyY+" "+GyZ+" "+state);
  Serial.println(sensorReading+" "+AcY+" "+AcX+" "+AcZ+" "+GyX+" "+GyY+" "+GyZ+" "+state);
}
```
<hr/>   

### 1-3 Unity에서 Arduino를 시리얼포트로 받는 방법

![unity ](https://user-images.githubusercontent.com/62869017/122768881-fdf1ff80-d2de-11eb-8ea9-f85d17b2a3a4.png)












