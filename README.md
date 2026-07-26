# arduino-temperature-sensor
What the project is:
This is a documentation of a project that detects the temperature of a 3-W LED using a thermistor. The 3-W LED's brightness increases (using PWM) from a pull-up resistor (when the yellow button is pressed a voltage of 0 is read from a digital input which is coded to slightly increase the brightness of the 3-W LED). The exact same concept applies to the dimming of the 3-W LED, however it is dimmed with the blue button. The green LED is turned on so long as the temperature is within a "safe" range (the temperature levels never actually reach unsafe levels), a yellow LED turns on alongside a double beep sound if the temperature reaches a "warning level," which in this code is set to 90°F but can easily be changed in the code, and a red LED turns on alongside an alarm if the temperature reaches a "hot level". Eventually, if the temperature is too hot, then the 3-W LED will completely turn off.

Demonstration Video:
There is a video demonstrating the project works in the repository below the README.md. It is titled "engineering project video FINAL". If it doesn't let you see it 
