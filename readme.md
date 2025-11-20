# CubeIDE 설정 가이드

## 보드 선택법

### *** NUCLEO F103RB 기준으로 작성 ***

file -> creat project -> STM32 Project -> Board select -> commercial Part Number에 NUCLEO F103RB 혹은 103 입력 -> Next

그 후, project name 입력 후 Finish

<img width="1359" height="1011" alt="image" src="https://github.com/user-attachments/assets/da96d69c-0ad6-47c5-8497-358aba422c85" />


## 기초 ioc 설정법

### pinout -> RCC

외부 클럭을 사용하지 않는다면 HSE, LSE 전부 Disable

<img width="1580" height="774" alt="image" src="https://github.com/user-attachments/assets/e8560d2f-59eb-4d00-8b69-b1a271bedad5" />

### GPIO

기본적으로 GPIO mode, GPIO Pull-up/ Pull-down만 살펴보자.

push botton은 GPIO mode가 External interrupt Mode with Rising edge trigger detection --> 버튼이 눌릴 때마다 이벤트 발생

LED는 GPIO mode가 Output Push Pull --> vdd, gnd와 연결되어서 1 혹은 0으로 아웃풋 발생

pull up/ pull down은 기본적으로 둘 다 no지만, 만약 다른 gpio에서는 pull up, pull down 저항에 따라서 이를 체크해줘야 함.

<img width="937" height="707" alt="image" src="https://github.com/user-attachments/assets/62c18260-87e9-4152-b1ff-c34d29a9f834" />


### Analog

기본적으로 gpio는 디지털 신호로 0 or 1.

그러나, Analog는 말 그대로 아날로그 신호 발생.

ADC1, ADC2가 있고, ioc 핀 할당에서 지정 가능.

단, 핀마다 할당 가능한 ADC IN #이 정해져 있음. (PC3 pin 기준, ADC 1 IN13, ADC 2 IN 13)

<img width="1577" height="801" alt="image" src="https://github.com/user-attachments/assets/18aee486-f6b5-45a6-94cb-e01daa92674e" />

### Timers

기본적으로 내장된 clock을 이용하여 여러가지를 수행 가능.

일반적으로, clock source에서 internal clock check.

channel에서는, 여러가지가 있지만 PWM을 사용 가능. (Channel은 analog와 같이 정해진 pin에서만 사용 가능)

*** Internal clock ***

핵심은 Prescale, Counter Period (AutoReload Register ARR)

Prescale : 일종의 Clock divier. NUCLEO F103RB 기준, 64MHz 에서 Prescale + 1의 값으로 나눈 클럭을 출력함. ex) Prescale : 63 --> 1MHz clock )

Counter Period (ARR) : 클럭을 저장하는 레지스터. 최대 Counter Period + 1까지 저장하고, 다시 0부터 시작. ex) Prescale : 63, Counter Period : 999 --> 1MHz의 clock이므로, 초당 100만번의 클럭 -> 1000번의 클럭까지 세므로, 1ms 단위로 레지스터 초기화

<img width="809" height="786" alt="image" src="https://github.com/user-attachments/assets/f543de20-723b-480c-909c-c1a4df5bc1c9" />


### UART

기본적으로 UART2 활성화 --> USB 통신

Bluetooth 통신은 UART3 활성화를 해야만 함.

통신을 할 때, Baud Rate와 Word Length 등 값을 맞춰줘야 통신 가능.

<img width="797" height="723" alt="image" src="https://github.com/user-attachments/assets/d3dcde86-4cb1-4ce8-91e0-470acebc5206" />



