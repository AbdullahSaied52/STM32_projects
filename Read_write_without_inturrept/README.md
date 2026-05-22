\# STM32 UART Interfacing \& GPIO Control 📑🚀



This repository contains an embedded C application developed for \*\*STM32 microcontrollers\*\* (using STM32CubeMX and the \*\*HAL (Hardware Abstraction Layer) drivers\*\*). The project demonstrates bidirectional serial communication via \*\*UART\*\* synchronized with hardware input/output triggers (\*\*GPIO\*\*).



\---



\## 📌 Project Architecture \& Logic



The firmware runs an infinite loop that constantly polls the state of a digital input pin. 



\* \*\*Default State (Button Released):\*\* An LED connected to `PA5` remains turned \*\*ON\*\*.

\* \*\*Active State (Button Pressed):\*\* 1. The LED on `PA5` turns \*\*OFF\*\*.

&#x20; 2. The MCU transmits a prompt over UART1: `"please enter anything:"`.

&#x20; 3. The MCU halts and waits to receive a response string (up to 100 characters) via UART with a \*\*5-second timeout\*\*.

&#x20; 4. The MCU echoes back the received string along with a confirmation message: `"you entered: \[user\_string]"`.

&#x20; 5. A `500ms` debounce/stabilization delay is applied before checking the button state again.



\---



\## 🛠️ Hardware Configuration Details



\### 1. GPIO Pin Mapping

| Peripheral | Pin | Mode | Configuration | Description |

| :--- | :--- | :--- | :--- | :--- |

| \*\*GPIOA\*\* | `PA5` | `GPIO\_MODE\_OUTPUT\_PP` | Push-Pull, No Pull | Actuates an external/internal LED |

| \*\*GPIOA\*\* | `PA6` | `GPIO\_MODE\_INPUT` | Pull-Down internal | Connected to a tactile push button |



\### 2. USART1 Configuration

\* \*\*Baud Rate:\*\* `9600` bits/s

\* \*\*Word Length:\*\* 8 Bits (including parity)

\* \*\*Stop Bits:\*\* 1 Stop Bit

\* \*\*Parity:\*\* None

\* \*\*Hardware Flow Control:\*\* None

\* \*\*Mode:\*\* Asynchronous RX/TX enabled



\---



\## 💻 Code Overview (Main Loop)



The core operations are embedded within the `while(1)` block utilizing the following HAL APIs:

\* `HAL\_GPIO\_ReadPin()` to sample the button state.

\* `HAL\_UART\_Transmit()` to push telemetry strings to the host computer.

\* `HAL\_UART\_Receive()` blocking call to capture incoming serial data streams.



```c

GPIO\_PinState p\_s = HAL\_GPIO\_ReadPin(GPIOA, GPIO\_PIN\_6);

if(p\_s == 1)

{

&#x20;   HAL\_GPIO\_WritePin(GPIOA, GPIO\_PIN\_5, GPIO\_PIN\_RESET);

&#x20;   HAL\_UART\_Transmit(\&huart1, (uint8\_t \*)str, sizeof(str), 100);

&#x20;   HAL\_UART\_Receive(\&huart1, (uint8\_t \*)rx, sizeof(rx), 5000);

&#x20;   

&#x20;   HAL\_UART\_Transmit(\&huart1, (uint8\_t \*)c, sizeof(c), 100);

&#x20;   HAL\_UART\_Transmit(\&huart1, (uint8\_t \*)rx, sizeof(rx), 100);

&#x20;   HAL\_Delay(500);

}

