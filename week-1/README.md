Project – Week 1
 
BLINKING OF LED : SINGLE LED ,ARRAY OF LED, MATRIX OF LED, LED INTENSITY CONTROLLER

 Hardware Required
- Arduino UNO board
- 1 x Potentiometer  
- 1 × LED (any color)  
- 1 × 220Ω resistor  
- Breadboard  
- Jumper wires  
- USB cable (for uploading code)

 Circuit Diagram Description
- Connect the long leg (anode) of the LED to digital pin 13 of the Arduino through a 220Ω resistor.  
- Connect the short leg (cathode) of the LED directly to GND.  
- The resistor limits current to protect the LED.  
- Power is supplied via the Arduino’s USB connection.

 How to Upload Code

 Expected Output
- The LED connected to pin 13 will blink ON and OFF at a fixed interval (e.g., 1 second).  
- This demonstrates basic digital output control using Arduino.

 Troubleshooting Tips
1. LED not blinking?
   - Check polarity: long leg → resistor → pin 13, short leg → GND.  
2. Upload error? 
   - Ensure correct COM port is selected in Arduino IDE.  
3. LED too dim or not visible? 
   - Verify resistor value (220Ω recommended). Using a higher resistance may reduce brightness.  
4. Board not detected? 
   - Install Arduino drivers or try a different USB cable/port.  
5. Code compiles but no output? 
   - Confirm that pin number in code matches the physical connection.
