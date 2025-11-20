### LED Blink

## ioc setting

ioc 초기값에 led가 설정되어 있으므로, RCC 설정 외 바꾸지 않고 Project -> Generate code

<img width="1579" height="771" alt="image" src="https://github.com/user-attachments/assets/1e85b709-d334-477c-b04e-3f74d1d64b6d" />



## main.c 설정

int main (void) 안에 있는, while(1)에 다음과 같은 코드를 입력한다.

HAL_GPIO_WrittePin(LD2_GPIO_Port, LD2_Pin, 1);
HAL_Delay(원하는 시간, 단위는 ms);
HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, 0);

혹은,

HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
HAL_Delay(원하는 시간, 단위는 ms);

## 해석

HAL_GPIO_WritePin : 지정한 포트, 지정한 핀에 대해서 0 혹은 1의 신호를 입력.

HAL_Delay : 지정한 시간만큼 대기. 기본적으로 블로킹 함수 (이 함수가 돌아가는 시간 동안 아무 것도 못 함)

HAL_GPIO_TogglePin : 지정한 포트, 지정한 핀에 대해서 신호를 반전. (0이면 1, 1이면 0...)

LED에 1의 신호가 들어갔다가, 지정한 시간만큼 대기 후, 0의 신호가 들어감. 이를 무한 반복

## 팁

중간까지 친 다음, CTRL + SPACE를 누르면, 자동 완성 혹은 관련 있는 코드를 찾을 수 있음.

<img width="531" height="319" alt="image" src="https://github.com/user-attachments/assets/2303b0a4-8751-4c4e-8461-bdf833dbd378" />



<img width="580" height="290" alt="image" src="https://github.com/user-attachments/assets/82fa1df2-e291-46b6-a759-2159cdc35754" />



