# HC-SR04 초음파 거리 측정기에 대해 다룹니다.

## HC-SR04

<img width="1420" height="798" alt="image" src="https://github.com/user-attachments/assets/fa30cec1-00ed-4c9a-890e-1ba16cb4d75a" />

tx에서 40KHz의 초음파를 쏘고, 이 초음파가 반사되어서 rx로 들어가는 구조.

기본적으로 세팅은 timer를 이용.

<img width="468" height="664" alt="image" src="https://github.com/user-attachments/assets/58010eed-b0f7-4133-bbf5-897a710c6a82" />

<img width="638" height="570" alt="image" src="https://github.com/user-attachments/assets/73f8dbc5-f1fc-4b92-a309-bb4947ee9a7a" />

### 여기서는 bluetooth 통신을 위해 UART3를 사용하였지만, bluetooth를 사용할 게 아니라면 제거해도 무방함.




## 코드 분석

```

/* USER CODE BEGIN Includes */

#include <stdio.h>
#include <stdlib.h>

/* USER CODE END Includes */

#ifdef __GNUC__
#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif /* __GNUC__ */
PUTCHAR_PROTOTYPE
{ if (ch == '\n')
	HAL_UART_Transmit (&huart3, (uint8_t*) "\r", 1, 0xFFFF);
HAL_UART_Transmit (&huart3, (uint8_t*) &ch, 1, 0xFFFF);

return ch;

}

```

### 측정값을 터미널로 받기 위해서 꼭 넣어야함.

```

void timer_start(void)
{
   HAL_TIM_Base_Start(&htim1);
}

```

타이머 작동 시작 함수. 이게 없으면 타이머 기반 작동 안 함.

```

void delay_us(uint16_t us)
{
   __HAL_TIM_SET_COUNTER(&htim1, 0);
   while((__HAL_TIM_GET_COUNTER(&htim1))<us);
}

```

타이머를 이용한 μs 단위의 시간을 만드는 함수. 이 때문에 prescale 63으로 설정해야만 함.

```

void trig(void)
   {
       HAL_GPIO_WritePin(Trig_GPIO_Port, Trig_Pin, 1);
       delay_us(10);
       HAL_GPIO_WritePin(Trig_GPIO_Port, Trig_Pin, 0);
   }


```

10μs 단위로 tx에서 초음파 발사

```

long unsigned int echo(void)
   {
       long unsigned int echo = 0;

       while(HAL_GPIO_ReadPin(Echo_GPIO_Port, Echo_Pin) == 0){}
            __HAL_TIM_SET_COUNTER(&htim1, 0);
            while(HAL_GPIO_ReadPin(Echo_GPIO_Port, Echo_Pin) == 1);
            echo = __HAL_TIM_GET_COUNTER(&htim1);
       if( echo >= 240 && echo <= 23000 ) return echo;
       else return 0;
   }

```

echo 0 -> 1이 될 때 까지의 시간을 측정. 240μs 미만 23ms 초과는 측정하지 않음.

### 다음은 메인에 넣어야 함.


```

timer_start();

```

타이머 시작 함수 실행.

```

 trig();
	  int echo_time = echo();
	  if(echo_time != 0)
	  {
		  int dist = (int)(17 * echo_time / 100);
		  printf("RDistance%d\r\n", dist);
	  }
	  else
		  printf("Out of Range!\r\n");


```

tx에서 초음파를 쏘고, echo_time이 0이 아니라면, 그 값을 계산한 후 출력.

계산값은 음속에 대한 값.

