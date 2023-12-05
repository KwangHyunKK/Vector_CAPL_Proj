### CAPL

<hr>

#### 0. CAPL 개요

- Vector 사에서 개발된 프로그래밍 언어
- CANoe와 CANalyzer에서 지원

#### 1. CAPL 기초

- CAPL(Communication Access Programming Language) 의 약어
- C언어의 문법에 상당한 바탕

##### C언어와 다른 독특한 특성

- **Event Driven** 방식
    - 개별 함수의 집합으로 프로그램 구성
    - 각 함수가 메시지의 수신, 신호의 변화, 타이머 만료 및 "환경"의 변화 등과 같이 분석대상인 시스템 내에서 발생하는 이벤트에 따라 동작
    - EX) **EngineState** 메시지에 대응하기 위해서 "Onmessage EngineState" 사용

- CANdb++
    - 분석 대상 시스템의 컨셉에 대해 특화된 데이터베이스 사용

- **포인터** 타입 제거
    - associative array와 같은 기능에 대한 대안 확보

##### C 언어와 공통된 특징

- **항상 컴파일 된다** : 효율적인 실행코드와 유연한 머신 코드로 변환

#### 2. 예제

```
// 전역 변수와 상수를 정의하는 영역
// 프로그램에서 전역으로 정의되며, 프로그램의 모든 곳에서 사용 가능, CANoe의 다른 프로그램에서는 사용할 수 없다.
variables 
{
    const long kOFF = 0;
    const long kON = 1;
}

// 메시지 이벤트 처리 
// CAN 통신의 경우, CAN 컨트롤러의 Tx 혹은 Rx 인터럽트 시점, 즉 메시지의 정확한 전송 완료 직후 on message 발생
// 이벤트 내에서 메시지 명은 this
on message EngineState
{
    @sysvar::Engine::EngineSpeedDspMeter = this.EngineSpeed / 1000.0;
}

on message LightState
{
    if(this.dir == RX)
    {
        SetLightDsp(this.HeadLight, this.FlashLight);
    }
    else
    {
        write("Error : LightState TX received by node %NODE_NAME%");
    }
}

SetLightDsp(long headLight, long hazardFlasher)
{
    long tmpLightDsp;

    tmpLightDsp = 0;
    if(headLight == kON)
        tmpLightDsp = 4;
    if(hazardFlasher == kON)
        tmpLightDsp += 3;
    @sysvar::Lights::LightDisplay = tmpLightDsp;
}
```

##### 2.1 작업 설명

- DB에 기술된 CAN 버스의 모든 요소, 즉 버스 노드, 메시지와 시그널 관측
- EngineState 메시지 수신 => 시그널이 패널에 연결
- LightState 메시지 => HeadLight 와 FlashLight 시그널 표시 연결


#### 3. 기초적인 CAPL Event

##### 3.1 7가지 이벤트

- on start()
- on pre-start()
- on stop()
- on key 'key-name'()
- on envvar 'Envvar name'()
- on message messagename()
- on timer()


##### 3.2 on start

- Measurement start 아이콘을 클릭해서 CANoe에서 시뮬레이션을 시작하는 경우, start() 함수의 코드 일부가 실행

##### 3.3 on pre-start

- start 이벤트 전에 실행, 변수 초기화, 딜레이 설정 등에 유용하게 사용 가능
- setStartdelay : pre-start()의 고유 기능. 타이밍 매개변수를 테스트하는데 사용 가능

##### 3.4 on stop

- stop 이벤트는 CANoe, CANalyzer 창에서 stop 버튼 시에 발생하는 이벤트

##### 3.5 on key 'key-name'

- 'key-name' 해당하는 키를 누르면 key 이벤트 발생

##### 3.6 on envvar "Envvar name"

- 환경변수 변경 시 이 이벤트 호출

##### 3.7 on message "messagename"

- messagename에 지정된 CAN 메시지가 수신될 때 발생


##### 3.8 on timer

- settimer() 호출로 트리거되는 특수 변수
- canceltimer() 호출로 중지 가능
- 타이머는 variables 섹션에서 선언
- mstimer 설정 시 밀리초 단위로 값을 가져오고, 해당 값 완료 => 타이머 이벤트 발생

#### 4. 기본적인 팁

- CAPL 테스트 모듈은 Maintest()를 가지고 있어 순차적으로 테스트 진행
- CAPL 파일은 .can
- Inclue 파일은 .cin
- 컴파일한 파일 형식은 .cbf
- 함수 사용법
    - CANoe 도움말 CAPL 도움말을 참고
    - 도움말 목차를 보고 선택
    - 함수 이름을 알면 F1을 눌러서 찾을 수 있다.


#### 5. [Test Script 작성하는 방법](https://nvdungx.github.io/CAPL-script/)

