# ADC-single-sensor

# STM32 Light-Controlled LED System

##  Overview
This project demonstrates a simple embedded application on an **STM32 microcontroller** that reads ambient light levels using an **Analog-to-Digital Converter (ADC)** and controls an onboard **LED** based on a light intensity threshold. It also transmits the measured light value over **UART** for debugging and monitoring.

### Key Features
* **ADC Integration:** Reads analog voltage from a light sensor (e.g., LDR or photoresistor) via ADC Channel 0.
* **LED Control:** Turns an LED (connected to **GPIOA Pin 5**) ON when the light level is below a threshold (`lux < 500`).
* **Serial Output (UART):** Transmits the raw ADC light value (`lux`) over **USART2** at 115200 baud.
* **HAL Library:** Developed using STMicroelectronics' Hardware Abstraction Layer (HAL).

##  Hardware Used (Typical)
* **Microcontroller:** STM32 board (e.g., STM32F4-Discovery, Nucleo, etc.)
* **Light Sensor:** LDR/Photoresistor connected to **ADC1 Channel 0 (PA0)**.
* **LED:** Connected to a digital output pin, typically **GPIOA Pin 5** (often the onboard LED on Nucleo/Discovery boards).
* **Serial Terminal:** For viewing the UART output.

##  How it Works
1.  **Initialization:** The system clock, GPIOs (for the LED and UART), and the ADC are configured.
2.  **ADC Read:** In the infinite loop, the ADC conversion is started, polled for completion, and the raw 12-bit value (`lux`) is read.
3.  **UART Transmit:** The light value is formatted into a string and sent over UART.
4.  **Threshold Check:**
    * If `lux < 500` (low light detected), the LED on **PA5** is set **HIGH** (turned ON).
    * Otherwise, the LED is set **LOW** (turned OFF).
5.  **Delay:** A brief 200ms delay is introduced before the next cycle.

### Code Snippet (Core Logic)
```c
while (1)
{
    HAL_ADC_Start(&hadc1);
    HAL_ADC_PollForConversion(&hadc1, 20);
    lux = HAL_ADC_GetValue(&hadc1);
    sprintf(msg, "value of light: %d\r\n", lux);
    HAL_UART_Transmit(&huart2, (uint8_t*)msg, strlen(msg), HAL_MAX_DELAY);

    if (lux < 500)
    {
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET); // LED ON (Dark)
    }
    else {
        HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET); // LED OFF (Bright)
    }

    HAL_Delay(200);
}
