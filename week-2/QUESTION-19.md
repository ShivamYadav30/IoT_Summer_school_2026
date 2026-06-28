## Difference between `analogWrite()` and `analogRead()` in Arduino

| Feature     | `analogWrite()`                                | `analogRead()`                        |
| ----------- | ---------------------------------------------- | ------------------------------------- |
| Purpose     | Outputs a PWM signal (simulates analog output) | Reads an analog voltage from a sensor |
| Pin Type    | PWM pins (3, 5, 6, 9, 10, 11 on Arduino UNO)   | Analog input pins (A0–A5)             |
| Value Range | 0–255                                          | 0–1023 (10-bit ADC)                   |
| Returns     | No value                                       | Integer (0–1023)                      |
| Example     | `analogWrite(9, 128);`                         | `int value = analogRead(A0);`         |

---

## What is PWM?

**PWM (Pulse Width Modulation)** is a technique used to simulate an analog output by rapidly switching a digital pin **ON** and **OFF**. The average voltage depends on the **duty cycle**.

* **0** → Always OFF (0 V)
* **128** → 50% duty cycle (about 2.5 V average)
* **255** → Always ON (5 V)

PWM is used because the Arduino UNO has **no true analog output**. It creates a variable average voltage using digital pulses.

---

## Practical IoT Example of `analogRead()`

**Smart Plant Watering System**

* A soil moisture sensor is connected to **A0**.
* Arduino reads the soil moisture level using `analogRead()`.
* If the soil is dry, it turns on a water pump and can send the data to a cloud platform using an ESP8266 or ESP32.

Example:

```cpp
int moisture = analogRead(A0);
```

---

## Practical IoT Example of `analogWrite()`

**Smart Street Light**

* An LDR detects ambient light.
* Arduino adjusts the brightness of an LED street light using PWM with `analogWrite()`.
* At night, the LED becomes brighter; during the day, it becomes dim or turns off.

Example:

```cpp
analogWrite(9, 180);   // Set LED brightness
```

---

### Summary

* **`analogRead()`** → Reads analog sensor values (0–1023).
* **`analogWrite()`** → Generates a PWM signal (0–255) to control devices like LEDs and motors.
* **PWM** is used to control **brightness, motor speed, and power** by varying the duty cycle of a digital signal.
