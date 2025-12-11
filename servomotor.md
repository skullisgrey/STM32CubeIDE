# servomotor에 대해 다룹니다.

## Prescale 1279, counter period(ARR) 999로 설정.<br><br>두 개의 서보모터를 다루므로, 2개의 타이머와 2개 pwm channel 사용


## 코드 분석

```

/* USER CODE BEGIN Includes */

#include <stdio.h>

/* USER CODE END Includes */

#ifdef __GNUC__
#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif /* __GNUC__ */

PUTCHAR_PROTOTYPE
{
  if (ch == '\n')
    HAL_UART_Transmit (&huart2, (uint8_t*) "\r", 1, 0xFFFF);
  HAL_UART_Transmit (&huart2, (uint8_t*) &ch, 1, 0xFFFF);

  return ch;
}

```

터미널에서 키 입력을 통한 제어를 위한 uart 통신

```

#define MAX 130
#define MIN 30
#define CENTER 80
#define STEP 1

```

서보 모터의 최소, 최대 각도와 중앙값 정의 및 한 번에 회전하는 각도 정의

```

uint16_t pwm_to_angle(uint8_t pwm_value)
{
 return ((uint16_t)(pwm_value - MIN) * 1800) / (MAX - MIN);
]

void display_servo_status(uint8_t pan, uint8_t tilt)
{
  uint16_t pan_angle = pwm_to_angle(pan);
  uint16_t tilt_angle = pwm_to_angle(tilt);

  printf("Pan: %3d (%3d.%d°) | Tilt: %3d (%3d.%d°)\r\n",
         pan, pan_angle/10, pan_angle%10,
         tilt, tilt_angle/10, tilt_angle%10);
}

```

서보 각도 및 현재 각도 출력

### 메인에 넣어줘야 하는 코드


```

 HAL_TIM_PWM_Start(&htim2, TIM_CHANNEL_1);
 HAL_TIM_PWM_Start(&htim3, TIM_CHANNEL_1);

 __HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, pos_pan);
 __HAL_TIM_SET_COMPARE(&htim3, TIM_CHANNEL_1, pos_tilt);

 display_servo_status(pos_pan, pos_tilt);

```

타이머 시작 및 작동 시작 시 초기 위치 설정

### 반복문에 넣어야 하는 코드

```

 if(HAL_UART_Receive(&huart2, &ch, sizeof(ch), 10) == HAL_OK)
	      {
	        // 명령 처리
	        if(ch == 's')  // Down
	        {
	          printf("Command: Down\r\n");
	          if(pos_tilt + STEP <= MAX)
	            pos_tilt = pos_tilt + STEP;
	          else
	            pos_tilt = MAX;
	        }
	        else if(ch == 'w')  // Up
	        {
	          printf("Command: Up\r\n");
	          if(pos_tilt - STEP >= MIN)
	            pos_tilt = pos_tilt - STEP;
	          else
	            pos_tilt = MIN;
	        }
	        else if(ch == 'a')  // Left
	        {
	          printf("Command: Left\r\n");
	          if(pos_pan + STEP <= MAX)
	            pos_pan = pos_pan + STEP;
	          else
	            pos_pan = MAX;
	        }
	        else if(ch == 'd')  // Right
	        {
	          printf("Command: Right\r\n");
	          if(pos_pan - STEP >= MIN)
	            pos_pan = pos_pan - STEP;
	          else
	            pos_pan = MIN;
	        }
	        else if(ch == 'i')  // Center
	        {
	          printf("Command: Center\r\n");
	          pos_pan = CENTER;
	          pos_tilt = CENTER;
	        }
	        else
	        {
	          printf("Invalid command: %c\r\n", ch);
	          continue;
	        }

	       
	        __HAL_TIM_SET_COMPARE(&htim2, TIM_CHANNEL_1, pos_pan);
	        __HAL_TIM_SET_COMPARE(&htim3, TIM_CHANNEL_1, pos_tilt);

	        
	        display_servo_status(pos_pan, pos_tilt);

	        HAL_Delay(20); 

```

1바이트 명령을 받음. 최대 대기시간은 10ms

명령을 받으면 명령에 따라 서보 모터가 회전. 정해진 명령 외의 명령이 들어오면 다시 while의 위에서부터 시작.
