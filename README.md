🐗 농작물 피해 예방 야생동물 탐지 SMART CCTV 시스템
===
카메라로 객체를 **추적 및 탐지**하여 사용자에게 **실시간으로 알림**을 주는 SMART CCTV 시스템입니다.     
**라즈베리파이**로부터 서버로 영상을 실시간으로 받아와 객체 추적 및 탐지 모델을 적용하였습니다.    
***

## 1. Technology
* 객체 추적 및 탐지
  * `SORT/YOLO`: 객체 추적 알고리즘은 SORT와 `yolov4-tiny`를 사용
* 데이터베이스
  * `Sqlite`
* 비동기 처리를 통한 실시간 탐지값 저장
  * `Ajax`, `JQuery`
* 푸시 알림
  * `FCM token`  
  
![image](https://user-images.githubusercontent.com/74829786/177007491-443f47af-f748-4e31-96d5-f79d6652553c.png)


***

## 2. 실행방법
라즈베리 파이에서 동영상을 촬영하고, 이를 서버에서 받아 Object Detection을 하는 프로그램입니다.    
* [imagezmq](https://github.com/jeffbass/imagezmq)를 사용하여 파이 카메라로 촬영된 영상을 PC 서버로 가져옵니다.
* **`pi-server`**: **라즈베리파이에 다운로드**합니다. 파이 카메라로부터 촬영된 영상을 직접 가져와 PC서버로 전송하는 코드입니다.
* **`stream-server`**: **PC에 다운로드**합니다. 라즈베리파이로부터 받은 영상을 웹에서 스트리밍하여 볼 수 있도록 합니다.

### 2.1. Gettting started
프로그램을 실행시키기 위하여 아래의 코드를 터미널에 입력하여 라이브러리들을 설치합니다.
```Shell
$ pip install -r requirements.txt
```

### 2.2. How to use
#### ⚠ 주의사항
* [server.py](https://github.com/thispath98/Animal-Detection-using-Raspberry-Pi/tree/master/server.py)를 우선적으로 PC, 개인 노트북, 서버에서 실행합니다. 
* 그 이후 라즈베리 파이에서 [cam.py](https://github.com/thispath98/Animal-Detection-using-Raspberry-Pi/tree/master/cam.py)를 실행합니다.
* 서버와 라즈베리 파이는 **동일한 네트워크 환경**(공유기 등)에 존재해야 합니다.

#### 📌 Server
##### optional arguments

```Shell
$ python server.py -h
usage: server.py [-h] [--input INPUT] [--weights WEIGHTS]
                 [--configure CONFIGURE] [--label LABEL]
                 [--confidence CONFIDENCE] [--threshold THRESHOLD]
                 [--frame FRAME]

Server gets Raspberry pi's capture through zmq

optional arguments:
  -h, --help            show this help message and exit
  --input INPUT         input video
  --weights WEIGHTS     yolo weights
  --configure CONFIGURE
                        yolo configure
  --label LABEL         coco class label
  --confidence CONFIDENCE
                        minimum confidence
  --threshold THRESHOLD
                        minimum threshold
  --frame FRAME         threshold of frame count
```

```Shell
$ python server.py --input {input}

e.g. python server.py              # 현재 pc에 연결된 웹캠으로 송출
    python server.py --input pi   # pi로부터 동영상 받아 송출
    python server.py --input data/car_on_road.mp4 # 동영상 송출
```

#### 📌 Raspberry Pi
##### optional arguments
```Shell
python cam.py -h
usage: cam.py [-h] --ip IP

Raspberry pi passes its video capture to server

optional arguments:
  -h, --help  show this help message and exit
  --ip IP     server IP that we want to pass
```

```Shell
python cam.py --ip {server_ip}

e.g. python cam.py --ip 192.168.0.19
```

```
#### 📌 실제 실행
python server.py --weights data/yolov4_tiny_class4.weights --configure data/yolov4_tiny_class4.cfg --label data/yolo_class4.names --input pi
```

***

## 3. Service Architecture
![image](https://user-images.githubusercontent.com/74829786/177006016-a8cc1024-5b61-4d97-a24b-16f0d63036e1.png)    
* 사전에 생성한 모델을 서버에 저장하고, 실시간으로 카메라로부터 수신되는 영상을 통해 **객체 탐지 및 추적 진행**합니다.
* 객체가 탐지되면 서버는 FCM호출, 영상 캡쳐, 데이터 처리를 진행합니다.
* 이를 통해 사용자는 총 다섯가지 서비스를 제공받게 됩니다.
  * 푸시 알림
  * 실시간 스트리밍
  * CCTV제어
  * 데이터 시각화
  * 탐지값 확인


### 3.1 실시간 스트리밍
* 사용자는 웹사이트와 앱을 통해 실시간으로 CCTV 화면을 확인할 수 있습니다.
* 앱은 인앱브라우저를 통해 웹페이지에 접속할 수 있습니다.    
![streaming_cctv_web](https://user-images.githubusercontent.com/74829786/177006770-3f15aafc-1c64-4849-9e39-6eebe6353b92.gif)


### 3.2. 데이터 시각화
* 야생동물 출현 패턴을 시각화하여 사용자가 적절한 조치를 취할 수 있도록 보조합니다.    
![web_datachart](https://user-images.githubusercontent.com/74829786/177006784-21865e2f-f9b8-4aac-a257-779d7ce14661.gif)

### 3.3. 탐지값 확인
* 실시간으로 탐지값을 저장합니다.
* 탐지 객체 사진, 날짜 및 시간, 이름을 저장합니다.
* 사용자가 탐지값을 확인할 수 있도록 하여 서비스에 대한 신뢰도를 제공합니다.    
![web_storage](https://user-images.githubusercontent.com/74829786/177006857-f2bb9714-bfde-4223-9bd6-801587c8000b.gif)


### 3.4. 실시간 푸시알림 및 3단계 알림 강도 조절
* 사용자는 언제든지 푸시알림을 실시간으로 수신할 수 있고 앱을 통해 알림 내역을 확인할 수 있습니다.
* 3단계 알림강도 조절이 가능합니다.
  * 0단계: OFF
  * 1단계: 같은 객체에 대해 한 번만 알림
  * 2단계: 프레임 내에 객체가 탐지되는 한 계속 알림    

<img src = "https://user-images.githubusercontent.com/74829786/177006916-b632ce35-3431-4bfb-982a-1a0385ecaae1.gif" width="30%" height="30%"><img src = "https://user-images.githubusercontent.com/74829786/177006920-c3688a5a-53e5-421f-9310-00a5900b533e.gif" width="30%" height="30%"><img src = "https://user-images.githubusercontent.com/74829786/177006927-2f96de48-d764-417c-9b06-7ed905bba68d.gif" width="30%" height="30%">

***

## 4. Reference
* *https://github.com/jeffbass/imagezmq*
* *https://github.com/eplatero97/Object-Detection-and-Tracking-with-YOLOv3-SORT*
