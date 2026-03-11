# Level-1-IoT-report
It's  my Marvel IoT leve 1 report


# Task 1 – Introduction to ESP32 & Basic Components

## Task 1a – Theory: Digital I/O & Interrupts

### Objective :Study GPIO configuration and interrupts for efficient input handling.

The ESP32 is a powerful microcontroller, and most people don’t use the raw 48-pin chip directly — they use the 36-pin ESP32 development board (ESP-WROOM-32). This board exposes only the most useful and safe pins from the chip, making it much easier to work with in real projects.

Even though the chip internally has 48 pins, the 36-pin version gives you all the important ones you actually need — GPIOs, power pins, communication pins, and analog inputs — while hiding the risky or internal-only pins.

What makes the ESP32 special is its pin multiplexing. Unlike older microcontrollers where each pin has a fixed job, most ESP32 pins can be assigned in software. The same pin can become UART, SPI, I²C, PWM, or a simple digital I/O, just by changing your code. This gives you extreme flexibility when designing hardware.

Inside the ESP32 you get a huge set of built-in features:

18 ADC channels to read sensors

2 DAC outputs to generate analog signals

3 SPI buses for fast data

3 UART ports for serial communication

2 I²C buses for displays and sensors

16 PWM channels for motors, LEDs, and control

2 I²S interfaces for audio

10 touch-sensitive GPIOs

Some pins are hard-wired to things like ADC and DAC, but most digital functions (UART, SPI, PWM, I²C) can be mapped to many different pins using the ESP32’s internal switching system.

However, not all pins behave the same. Some are involved in booting, some are connected to the flash memory, and some have built-in pull-ups or pull-downs that affect them when the board powers on. That’s why ESP32 pins are grouped into:

Safe pins – work normally for input and output

Caution pins – usable but can cause boot or startup issues

Restricted pins – best avoided for GPIO use

The 36-pin ESP32 board is designed so that most of the pins you see are safe to use, which is why it’s so popular for robotics, IoT, and embedded projects.

In simple words:
The ESP32 is not just a microcontroller — it’s a flexible, software-defined I/O machine. If you understand which pins are safe and which ones need care, you can make it do almost anything you imagine.

![alt text](https://i0.wp.com/randomnerdtutorials.com/wp-content/uploads/2018/08/ESP32-DOIT-DEVKIT-V1-Board-Pinout-36-GPIOs-updated.jpg?w=750&quality=100&strip=all&ssl=1)

##  ESP32 48-Pin GPIO Reference (ESP-WROOM-32)

| GPIO | Type | Input | Output | Main Function | Important Notes |
|------|------|--------|---------|----------------|------------------|
| 0 | Boot / GPIO | ✅ | ✅ | Boot mode select | Must be **LOW** to flash, outputs PWM at boot |
| 1 | UART0 TX | ❌ | ✅ | Serial debug | Prints boot logs |
| 2 | GPIO / LED | ✅ | ✅ | On-board LED | Must be **LOW or floating** at boot |
| 3 | UART0 RX | ✅ | ❌ | Serial input | Pulled HIGH at boot |
| 4 | GPIO | ✅ | ✅ | General purpose | Safe pin |
| 5 | GPIO / Boot | ✅ | ✅ | SPI CS (default) | PWM at boot, strapping pin |
| 6 | Flash | ❌ | ❌ | SPI Flash | ❌ DO NOT USE |
| 7 | Flash | ❌ | ❌ | SPI Flash | ❌ DO NOT USE |
| 8 | Flash | ❌ | ❌ | SPI Flash | ❌ DO NOT USE |
| 9 | Flash | ❌ | ❌ | SPI Flash | ❌ DO NOT USE |
| 10 | Flash | ❌ | ❌ | SPI Flash | ❌ DO NOT USE |
| 11 | Flash | ❌ | ❌ | SPI Flash | ❌ DO NOT USE |
| 12 | Boot / ADC | ✅ | ✅ | ADC2 | Must be LOW at boot |
| 13 | GPIO | ✅ | ✅ | SPI / PWM | Safe |
| 14 | GPIO | ✅ | ✅ | SPI / PWM | PWM at boot |
| 15 | Boot / GPIO | ✅ | ✅ | SPI CS | PWM at boot |
| 16 | GPIO | ✅ | ✅ | UART2 RX | Safe |
| 17 | GPIO | ✅ | ✅ | UART2 TX | Safe |
| 18 | GPIO | ✅ | ✅ | SPI SCK | Safe |
| 19 | GPIO | ✅ | ✅ | SPI MISO | Safe |
| 21 | GPIO | ✅ | ✅ | I²C SDA | Safe |
| 22 | GPIO | ✅ | ✅ | I²C SCL | Safe |
| 23 | GPIO | ✅ | ✅ | SPI MOSI | Safe |
| 25 | GPIO / DAC | ✅ | ✅ | DAC1 | Analog output |
| 26 | GPIO / DAC | ✅ | ✅ | DAC2 | Analog output |
| 27 | GPIO | ✅ | ✅ | ADC | Safe |
| 32 | GPIO / ADC | ✅ | ✅ | ADC | RTC capable |
| 33 | GPIO / ADC | ✅ | ✅ | ADC | RTC capable |
| 34 | ADC only | ✅ | ❌ | Analog input | Input-only |
| 35 | ADC only | ✅ | ❌ | Analog input | Input-only |
| 36 | ADC only | ✅ | ❌ | Analog input | Input-only (SVP) |
| 39 | ADC only | ✅ | ❌ | Analog input | Input-only (SVN) |

---

###  Pins we should NEVER use
GPIO **6–11** are connected to the **SPI flash memory**.  
Using them will stop the ESP32 from booting.

---

### ⚠️ Boot-sensitive pins

| GPIO | Why |
|------|-----|
| 0 | LOW = flashing mode |
| 2 | Must not be HIGH at boot |
| 5 | Boot strapping pin |
| 12 | HIGH breaks boot |
| 15 | Boot strapping pin |

## Task 1b – Simon Says Game using ESP32

### Objective: Familiarize with the ESP32 microcontroller by creating an engaging game.

Simon Says is a simple electronic memory game: the user has to repeat a growing sequence of colors. The sequence is displayed by lighting up the LEDs. 

![alt text](https://github.com/Souparno-Baidya/report_photos/blob/main/Screenshot%202026-01-12%20015006.png?raw=true)

In each turn, the game will play the sequence, and then wait for the user to repeat the sequence by pressing the buttons according to the color sequence. If the user repeated the sequence correctly, the game will play a "leveling-up", add a new color at the end of the sequence, and move to the next turn.

The game continues until the user has made a mistake. Then a game over sound is played, and the game restarts.
![alt text](https://github.com/Souparno-Baidya/report_photos/blob/main/Screenshot%202026-01-12%20015022.png?raw=true)

[Source code](https://github.com/souparna100/Simon-Says/tree/main)

# Task 2 – Temperature-Based Fan Control

## Objective: Automate fan (DC motor) control using a temperature sensor and relay.

In this experiment, an automated temperature regulation system was implemented using an ESP32 microcontroller, a DHT11 temperature sensor, and a DC motor controlled through a motor driver. The system continuously monitors ambient temperature and automatically controls the motor based on measured values. Temperature readings are displayed in real time through the serial monitor for observation and verification.

The DHT11 sensor is used to acquire environmental temperature data at regular intervals. The ESP32 reads the sensor output through its digital input interface and processes the received values in software. Each reading is printed to the serial monitor, allowing continuous tracking of temperature changes during operation.

A predefined temperature threshold is set within the program logic. When the measured temperature exceeds this threshold, the ESP32 sends a control signal to the motor driver module, which in turn activates the DC motor. If the temperature remains below the threshold, the motor stays off. This creates an automatic decision-based control mechanism driven by live sensor data.

Motor speed control is achieved using PWM signals generated by the ESP32. Depending on how much the temperature exceeds the threshold, the PWM duty cycle can be adjusted to vary motor speed, enabling proportional response instead of simple ON/OFF behavior.

This experiment demonstrates the integration of sensor data acquisition, serial monitoring, decision logic, and actuator control to realize a practical environmental automation system using embedded hardware and software.

<iframe width="951" height="535" src="https://www.youtube.com/embed/HRRVryyh9N4" title="Temperature based fan control" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Source code](https://www.youtube.com/watch?v=ryajaqTVhRg)




# Task 3
## 3a- Communication Protocols

## IoT Communication Protocols

## What is IoT Communication?
The **Internet of Things (IoT)** is all about devices talking to each other — sensors sending temperature, wearables tracking health, machines reporting status, and cities collecting data.  
To make this possible, devices use **IoT communication protocols** — basically the **languages** they use to exchange data over wired or wireless networks.

Choosing the right protocol depends on:
- **Distance** between devices  
- **Power usage** (battery vs plugged in)  
- **Obstacles** like walls, buildings, or terrain  
- **Data size & speed**  

Some protocols are built for **tiny battery sensors**, while others are for **fast cloud communication**.

---

## 🧩 Main IoT Communication Protocols

| Protocol | Best For | Power Use | Range | Speed | Style |
|--------|--------|---------|-------|-------|------|
| **MQTT** | Cloud IoT, ESP32, sensors | Very low | Internet | Medium | Publish–Subscribe |
| **HTTP** | Web & APIs | High | Internet | High | Request–Response |
| **CoAP** | Tiny sensors | Very low | Local / Internet | Low | Request–Response |
| **AMQP** | Industrial messaging | Medium | Internet | High | Broker-based |
| **Bluetooth (BLE)** | Wearables, phones | Ultra low | Short | Medium | Device-to-device |
| **Zigbee** | Smart homes | Very low | Medium | Low | Mesh network |
| **LoRaWAN** | Smart cities, farms | Ultra low | Very long | Very low | Gateway-based |
| **DDS** | Industrial systems | Medium | Local / Wide | Very high | Peer-to-peer |

---

## 🔹 MQTT — The IoT Standard
MQTT is the **most popular IoT protocol**.  
Devices send data to a **broker**, and other devices subscribe to receive it.

Perfect for:
- ESP32
- Cloud dashboards
- Sensors

Why it’s great:
- Uses **very little data**
- Works on **slow or unstable networks**
- Has **reliability levels**
- Supports **strong security (TLS, certificates)**

---

### I²C — Inter-Integrated Circuit

**Full form:** Inter-Integrated Circuit  
**Type:** Synchronous serial bus (2-wire)

**Uses:**
- Sensors  
- RTC modules  
- Displays  
- EEPROM  
- ADC/DAC chips  

**Lines:**
- SDA — Data  
- SCL — Clock  

**Features:**
- Only 2 wires required  
- Supports multiple devices on the same bus  
- Address-based communication  
- Master–slave architecture  

**Best for:**  
Low-speed peripherals and sensor networks.

---

### SPI — Serial Peripheral Interface

**Type:** High-speed synchronous serial

**Uses:**
- Displays  
- Flash memory  
- ADCs  
- DACs  
- RF modules  

**Lines:**
- MOSI — Master Out Slave In  
- MISO — Master In Slave Out  
- SCLK — Clock  
- CS — Chip Select (per device)  

**Features:**
- Very high speed  
- Full duplex communication  
- No addressing — uses chip select lines  
- Suitable for short-distance communication  

**Best for:**  
High-speed peripheral communication.

---
---

### 1-Wire Protocol

**Type:** Single data line bus

**Uses:**
- DS18B20 temperature sensors  
- Identification chips  

**Features:**
- One data wire plus ground  
- Each device has a unique ID  
- Minimal wiring required  

---

### CAN — Controller Area Network

**Type:** Robust multi-node communication bus

**Uses:**
- Automotive systems  
- Robotics  
- Industrial control  

**Features:**
- Noise resistant  
- Real-time communication support  
- Multi-master capability  
- Message priority arbitration  

---

### RS-232 / RS-485 — Serial Communication Standards

**Type:** Electrical serial communication standards

#### RS-232
**Uses:**
- Short-distance serial links  
- Legacy PC serial ports  

**Features:**
- Point-to-point communication  
- Limited cable length  

#### RS-485
**Uses:**
- Industrial networks  
- Long-distance communication  

**Features:**
- Multi-drop bus support  
- Better noise immunity  
- Longer cable distances  

---

### USB — Universal Serial Bus

**Uses:**
- Programming development boards  
- Data transfer  
- Power and data over same cable  

**Notes:**
- Many ESP32 development boards use a USB-to-UART bridge chip for programming and serial communication.

---

### SDIO — Secure Digital Input Output

**Type:** High-speed peripheral interface

**Uses:**
- SD cards  
- High-speed storage devices  
- Internal Wi-Fi modules (in some systems)  

**Features:**
- Faster than standard SPI mode for SD cards  
- Designed for high-throughput data transfer  


## 🔹 HTTP — The Web Protocol
HTTP is what websites use.  
IoT devices use it when talking to **web servers and REST APIs**.

Pros:
- Easy to use
- Works everywhere  
Cons:
- Uses more power & data  
Not ideal for battery devices.

---

## 🔹 CoAP — HTTP for Tiny Devices
CoAP is a **lightweight version of HTTP** designed for small, low-power devices.

- Runs on **UDP**
- Uses **very little bandwidth**
- Works well in **weak signal environments**

Perfect for:
- Sensors
- Space, satellite, remote IoT

---

## 🔹 AMQP — Industrial Messaging
AMQP is used when **reliable message delivery** is critical.

Used in:
- Banking
- Industrial systems
- Large-scale IoT servers  

It uses **message brokers** like RabbitMQ.

---

## 🔹 Bluetooth & BLE
Used for:
- Smart watches
- Phones
- Smart locks
- Fitness devices  

BLE is optimized for **very low power**, perfect for wearables.

---

## 🔹 Zigbee — Smart Home Network
Zigbee creates a **mesh network**, where devices forward messages for each other.

Used in:
- Smart lights
- Home automation
- Sensors  

Very power efficient, but not for fast data.

---

## 🔹 LoRaWAN — Long Distance IoT
LoRaWAN connects devices over **kilometers** using very little power.

Used in:
- Smart cities
- Agriculture
- Weather stations  

Batteries can last **10+ years**.

---

## 🔹 DDS — Industrial Data Network
DDS connects devices **directly** — no broker needed.

Used in:
- Robotics
- Military systems
- Industrial automation  

It shares data in real time between machines.

---

##  Final Thought
Different IoT problems need different languages.  
ESP32 + cloud → **MQTT**  
Smart home → **CAN**  
Wearables → **BLE**  
Smart cities → **LoRaWAN**



Choosing the right protocol is what makes an IoT system fast, reliable, and power-efficient.

This doesn't mean the other ones aren't useful but the use depe ds on the situation.

## 3b- Controlling LEDs Using MQTT
## Objective: Utilize any MQTT platform to establish publishing and subscribing functionalities.

This experiment explores the basics of the MQTT protocol and its working model in IoT communication systems. MQTT is a lightweight publish–subscribe messaging protocol where devices communicate through a central broker instead of sending messages directly to each other. A publisher device sends a control message (such as an LED ON/OFF command) to a specific topic on the broker, and all subscriber devices that are subscribed to that topic receive the message instantly. In this setup, when a message is published to control LEDs, the subscriber device reads the received payload and activates the corresponding LED accordingly. Through this activity, learners understand how MQTT enables efficient, low-bandwidth, real-time device communication and how it compares with other network and communication protocols used in IoT systems.

<iframe width="951" height="535" src="https://www.youtube.com/embed/1u9TokzrfCk" title="Mqtt protocol" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[Source code](https://github.com/souparna100/Mqtt-publish-practice/tree/maindef)

# Task 4 – Communication using I2C Protocol

## Objective: Both ESP32 boards should host web servers for bidirectional communication.

The I2C (Inter-Integrated Circuit) protocol is a widely used serial communication method that enables multiple devices to communicate using only two lines: SDA (Serial Data) and SCL (Serial Clock). It operates on a master-slave architecture, I2C is particularly useful for short-distance communication between microcontrollers and other devices such as displays and sensors. In this project, the ESP32 was configured as the master and hosted a web server through which a user could input a custom message. This message was then transmitted via the I2C bus to the Arduino UNO, which functioned as the slave. The message received by the other esp32 was shown in the LCD screen. I couldn,t complete it.

## Task 5 – Flashing Morse Code

Objective: Create a website that converts typed input (normal words) to Morse code. The LED should blink according to the Morse code generated.

In this task, I set up an ESP32 to host a web server where users can enter a message through a simple HTML interface.I didn't make a seperate website I created an webserver in the esp32 cpp code which gave me an ip address which when put browser gives the morse code interface websiite. The ESP32, connected to an LED, captures the message, converts it into Morse code, and then flashes the LED accordingly.

Each letter and number is represented by dots (short blinks) and dashes (lno blinks). This task demonstrated a real-life application of a web server for communication, allowing messages to be sent and displayed in Morse code through the LED.

[Source code](https://github.com/souparna100/Morse-code-generator-using-esp32)

<iframe width="951" height="535" src="https://www.youtube.com/embed/9pXfQtoJ2Gg" title="Morse code flashing" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Task 6 - Health Vitals Dashboard

Objective: Plot graphs of the vitals on a webserver for real-time monitoring.


In this task, I set up an ESP32 to host a web server where users on a graph like server. In this one we use the chart.js framework to get it. The problem I faced was that the sensor doesn't givee proper output.

This implements a real-time heart rate using a microcontroller connected to a bbp sensor. The measured physiological signals are processed and transmitted to a built-in webserver, where they are displayed graphically in a browser dashboard. The system enables remote, contact-based health monitoring using IoT principles.

[Sorce code](https://github.com/souparna100/Health-vital-dashboard-with-esp32/tree/main)

<iframe width="337" height="599" src="https://www.youtube.com/embed/GM-inXMmGyM" title="Bp sensor and health dashboard" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


## Task 7-  Fire Alarm System with SMS Alerts

Objective: Learn about fire detection and integrating SMS alert systems using APIs.

For this task, I used an temperature sensor and an ESP32. The flame sensor detects temperture emitted by fire using a  thermotransistor that responds to the temperature produced during combustion. When a flame is present, the sensor sends a digital high signal to the ESP32.

The ESP32, always connected to Wi-Fi, continuously monitors the sensor. When fire is detected, it immediately sends an email alert to notify selected recipients about the potential fire hazard.

[Source code](https://github.com/souparna100/FIRESMS/tree/main)

<iframe width="951" height="535" src="https://www.youtube.com/embed/USC8r5KQ37c" title="FIRE SMS" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

[def]: https://github.com/souparna100/Mqtt-publish-practice/tree/main