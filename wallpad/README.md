# Universal Wallpad Controller with RS485 

![Supports aarch64 Architecture][aarch64-shield] ![Supports amd64 Architecture][amd64-shield] ![Supports armhf Architecture][armhf-shield] ![Supports armv7 Architecture][armv7-shield] ![Supports i386 Architecture][i386-shield]

## About
네이버 HomeAssistant Korea 카페 <그레고리 하우스>님이 만든 nodejs 월패드 프로그램을 애드온으로 만든 것입니다.
코맥스, 삼성, 대림 아파트 월패드를 동작시킬 수 있는 nodejs 서버입니다. 
커스텀파일 설정이 가능해서 본인이 사용하는 nodejs 파일이 있다면 교체해서 쓰시면 됩니다.
Debian11 Bullseye기반의 Raspberry Pi OS 환경에서, HA Supervisor에서 동작가능하도록 탭스페이스에서 Fork하여 수정한 버전입니다.

## Version : 0.8.7

## Installation

1. 홈어시스턴트의 Hass.io > ADD-ON STORE에서 Add new repository by URL에 https://github.com/tabspacekr/addons 를 입력한 다음 ADD 버튼을 누릅니다.
2. ADD-ON STORE 페이지 하단에서 "Universal Wallpad Controller with RS485" 클릭합니다.
3. "INSTALL" 버튼을 누르면 애드온이 설치됩니다. 최대 약 10분 정도 소요. 
4. INSTALL 버튼위에 설치 애니메이션이 동작하는데 이것이 멈추더라도 REBUILD, START 버튼이 나타나지 않는 경우가 있습니다.
5. 이 애드온은 이미지를 내려받는 것이 아니라 직접 여러분의 Hassio에서 이미지를 만듭니다. 따라서 컴퓨터성능과 인터넷 속도에 따라서 시간이 좀 걸립니다. 
6. INSTALL 버튼을 누른다음 설치 애니메이션이 실행되면 제대로 설치중인 것입니다. INSTALL을 여러번 누르지 마시고 기다리다 지치면 브라우저 페이지를 리프리시 하세요. 
7. 애드온 페이지에서 Config을 본인의 환경에 맞게 수정합니다.
8. "START" 버튼으로 애드온을 실행합니다.

애드온을 최초 실행하면 share/모델명_wallpad.js 파일이 생성됩니다. 모델명_wallpad.js 파일은 본인의 월패드에 맞게 수정하셔도 됩니다. 수정한 후 애드온을 재실행하면 수정한 js 파일로 실행됩니다. 

## Configuration

Add-on configuration:

```yaml
model: commax
type: serial
sendDelay: 150
serial:
  port: /dev/ttyUSB0
  baudrate: 9600
  parity: none
socket:
  deviceIP: 192.0.0.1
  port: 8899
mqtt:
  server: 192.168.x.x
  username: id
  password: pw
  receiveDelay: 10000
```

### Option: `model` (필수)
월패드 모델명을 입력합니다. 가능한 모델명은 commax , samsung , daelim , hyundai 입니다.

### Option: `type` (필수)
통신 방법: serial 또는 socket 
socket은 samsung만 지원합니다. 

### Option: `serial` (옵션)
type: serial 로 설정한 경우 아래 옵션 사용

```yaml
  port: /dev/ttyUSB0  // 시리얼포트명
  baudrate: 9600      // 시리얼 통신 속도
  parity : none       // 패리티 체크 (none, even, odd 중 한 값)
  senddelay: 150      // 전송 딜레이 1/1000초 단위
```
socket을 사용하는 경우 위 값은 무시합니다.

### Option: `socket` (옵션) 
type: socket 로 설정한 경우 아래 옵션 사용
```yaml
  deviceIP: 192.0.x.x   // elfin과 같은 wifi to RS485 기기의 ip 주소
  port: 8899            // elfin과 같은 wifi to RS485 기기의 port 주소
```
serial을 사용하는 경우 위 값은 무시합니다. 
본 애드온에서는 코맥스는 socket을 지원 안합니다. 코맥스 소켓은 [HA카페 사람님의 애드온](https://cafe.naver.com/koreassistant/733)을 참고하세요.


### Option `MQTT` (필수)
```yaml
  server: 192.168.88.xx  // MQTT 서버
  username: id          // MQTT ID
  password: pw          // MQTT PW
  receivedelay: 10000	// 전송후 메시지 수신 지연 시간 1/1000초 단위
```

### Option: `customefile` (옵션)

개인화 설정을 하려면 이 옵션을 사용합니다. 자동으로 설치하려면 사용하지 않아도 됩니다. 

customfile: mygateway.js 

와 같이 설정하고 share/mygateway.js 파일을 넣어두면 애드온의 모든 설정을 무시하고 해당 파일로 nodejs를 실행합니다.
로그창에 Initializing mygateway.js... 과 같이 이름이 표시됨


### JS 파일의 수정

개인화 설정을 하려면 이 옵션을 사용합니다. 자동으로 설치하려면 사용하지 않아도 됩니다. 

본인이 기존에 사용중인 월패드용 js 파일이 있다면 해당 파일을 customfile 옵션으로 사용하세요. 
월패드에서 일부 기능을 수정하려면 share/모델명_wallpad.js 파일을 수정하시면됩니다. 

애드온의 옵션을 월패드 js에서 사용하려면 다음과 같습니다.
```js
const CONFIG = require('/data/options.json');  
//**** 애드온의 옵션을 불러옵니다. 옵션 파일의 위치는 변경 불가합니다. 
// 이후 CONFIG.mqtt.username 과 같이 사용가능합니다. 
// 사용가능한 옵션
CONFIG.type
CONFIG.sendDelay

CONFIG.serial.port
CONFIG.serial.baudrate
CONFIG.serial.parity

CONFIG.mqtt.server
CONFIG.mqtt.username
CONFIG.mqtt.password
CONFIG.mqtt.receiveDelay

//------------ 적용 예시 ------------
const CONST = {
    // 포트이름 설정
    portName: process.platform.startsWith('win') ? "COM6" : CONFIG.serial.port, 
    sendDelay: CONFIG.serial.senddelay,
    mqttBroker: 'mqtt://'+CONFIG.mqtt.server, 
    mqttDelay: CONFIG.mqtt.receivedelay,
    mqttUser: CONFIG.mqtt.username, 
    mqttPass: CONFIG.mqtt.password, 
//------------ 적용 예시 ------------
}
```
