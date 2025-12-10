# UART 통신에 대해 다룹니다.

## 기본적으로 stm32는 uart2가 활성화 됨.<br><br>uart2는 USB를 통한 통신이므로, Bluetooth 통신을 원한다면 Uart3 활성화 필요.

## 코드 분석

### 다음 코드는 메인 밖에 넣습니다.

```
#ifdef __GNUC__
#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
#else
#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
#endif /* __GNUC__ */
PUTCHAR_PROTOTYPE
{ if (ch == '\n')
	HAL_UART_Transmit (&huart2, (uint8_t*) "\r", 1, 0xFFFF);
HAL_UART_Transmit (&huart2, (uint8_t*) &ch, 1, 0xFFFF);

return ch;

}
```

만약 GNUC라는 함수가 정의되어 있다면, PUTCHAR_PROTOTYPE int __io_putchar(int ch)를 정의. 그렇지 않다면 PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)를 정의.

뒤의 int값은 반환값.

### 다음 코드는 메인의 반복문에 넣습니다.

```

HAL_UART_Receive(&huart2, &ch, 1, HAL_MAX_DELAY);
 printf("%c\n", ch);
 fflush(stdout);
 HAL_Delay(100);

```

1바이트 값을 받을 때까지 무한 대기.

1바이트 값을 받았다면, 그 값을 출력.

fflush(stdout)를 통하여 출력 버퍼를 초기화.

100ms 대기.

