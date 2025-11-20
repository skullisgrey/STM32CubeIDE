# Timbase LED blink

## ioc 설정

RCC -> Disable

Tim -> 하나의 tim을 활성화 후, internal clock check, prescale 63, counter period 999 설정

<img width="705" height="708" alt="image" src="https://github.com/user-attachments/assets/b5037d14-9ffc-4a68-98e3-58ae2de84170" />

<img width="716" height="726" alt="image" src="https://github.com/user-attachments/assets/8ed19c96-3aa2-4d4f-93d8-5c24bcff68ac" />

## main.c 설정

1. USER CODE BEGIN PV에 volatile int gTimerCnt;를 작성. // 시간 카운터를 위한 변수

2. int main (void)의 user code begin2에 다음과 같은 코드 작성.

if (HAL_TIM_Base_Start_IT (&htim3) != HAL_OK)
  {
	  Error_Handler ();

  }

3. int main (void) 바깥에, 다음과 같은 코드 작성.

void HAL_TIM_PeriodElapsedCallback (TIM_HandleTypeDef *htim)
{
	gTimerCnt++;
	if (gTimerCnt == 1000)
	{
		gTimerCnt = 0;
		HAL_GPIO_TogglePin (LD2_GPIO_Port, LD2_Pin);
  }
}


## 해석

volatile int : 일반적인 int와는 달리, 변수 값이 바뀔 수 있으니 최적화 시 무시하지 마라는 의미. // int : 정수값, integer

HAL_TIM_Base_Start_IT : 타이머를 시작하고 인터럽트를 활성화시켜주는 함수.
HAL_OK : 성공시

!=이므로, 위의 함수가 실패 시, Error_Handler 함수 호출 --> 오동작 방지용

HAL_TIM_PeriodElapsedCallback : 타이머 인터럽트가 발생시, 자동으로 호출되는 콜백 함수.

prescale이 63, counter period가 999이므로, NUCLEO F103RB 기준 1ms마다 호출. ( 인터럽트 주기 = {(prescale + 1) * (counter period +1 )}/internal clock

1ms마다 gTimerCnt의 값이 1씩 증가, 이 값이 1000이 되면 0으로 초기화 후, LED 토글 --> 1초마다 LED 토글

여기서 prescale, counter period 값을 변경해주면 led 토글 주기 변경 가능.

