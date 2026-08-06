# 3-W LED Temperature Safety System
## What the project is:
This is a documentation of a project that detects the temperature of a 3-W LED using a thermistor. The 3-W LED's brightness increases (using PWM) from an internal pull-up resistor (when the yellow button is pressed a voltage of 0 is read from a digital input which is coded to slightly increase the brightness of the 3-W LED). The exact same concept applies to the dimming of the 3-W LED, however it is dimmed with the blue button. The blue LED is turned on so long as the temperature is within a "safe" range (the temperature levels never actually reach unsafe levels), a yellow LED turns on alongside a double beep sound if the temperature reaches a "warning level," which in this code is set to 90°F but can easily be changed in the code, and a red LED turns on alongside an alarm if the temperature reaches a "hot level". Eventually, if the temperature is too hot, then the 3-W LED will be turned completely turn off and a 5V DC fan (connected to a TA6586) will turn on to cool the thermistor and 3-W LED. Bear in mind that only one LED is turned on at a time, for example if green is on then yellow and red are off. The 3-W LED needs too much current (far more than the Arduino can safely supply without the Arduino burning up, so a BJT npn was used). Each of the resistors used in this project were carefully calculated using Ohm’s law and the maximum safe current that could be used was the I (or current) component of Ohm’s law (a “worst case” scenario was assumed, e.g., for standard LEDs kirchhoff's 1st law wasn’t used and instead it was assumed that since it could be the case that the standard, small LED burned up (in which case the voltage drop is 0 across that diode) then it would be treated as such (this is a variation of Paul McWhorter’s way of calculating the necessary resistance).


## Demonstration Video:
https://youtu.be/_737u4TT6bs (small correction: at the 2:33 mark the serial monitor reads "⚠️ WARNING ⚠️ : LED is at UNSAFE temperature levels." However, this what cut off due to the video editor)

## Explaining each part:
1. Passive Buzzer
   - The passive buzzer is connected to pin 2 and a ground rail (polarity matters for the passive buzzer--so the + end is connected to pin 2)
   - When the code "tone" is ran the tone rapidly switches between 0 and 5 volts. The frequency (in Hz) determines how many times 0 and 5 V is       switched on and off and sent to the passive buzzer.
   - It makes a sound when you press the yellow or blue button. After i (which is the brightness level of the 3-W LED) passes 170 the frequency     of the tone sent to the buzzer increases for the yellow button to signal to the user that the LED's brightness is increasing rapidly. Once      the brightness, i, equals 255 the buzzer makes a distinct, high-pitch sound, which is caused by making the frequency much higher.
   - The passive buzzer sounds two beeps once the temperature surpasses the "temperature warning," which is also when the yellow LED turns on.
   - The passive buzzer sounds a repeating alarm once the temperature surpasses the "temperature hot," which is when the red LED turns on.

2. Buttons
   - The buttons use a pull-up resistor. A pull-up resistor is when there's a voltage constantly being read from a digital input pin. Whenever       the button is pressed the ground starts being read, which causes the digital input to read 0. This happens because the circuit is complete      whenever the button is pressed). Whenever the button isn't being pressed the digital pin reads 1, which happens because the circuit isn't       able to go to ground so the pin is reading the 5v being sent through the resistor.
   - The yellow button is connected to pin 12; the blue button is connected to pin 8.
   - Clicking yellow increases the brightness of the 3-W lED; clicking blue decreases the brightness of the 3-W LED.

3. 3 LEDs
  - The 3 LEDs are powered using digital outputs.
  - Blue is connected to pin 3, with a 220 ohm resistor between it and the p-type end. The n-type end is connected to a ground rail.
  - Yellow is connected to pin 4, with a 220 ohm resistor between it and the p-type end. The n-type end is connected to a ground rail.
  - Red is connected to pin 7, with a 220 ohm resistor between it and the p-type end. The n-type end is connected to a ground rail.
  - The LEDs won't shut off when the 3-W LED is forced to shut off. Instead, they will remain on at the temperatures previously explained. The      temperature will eventually decrease such that you will see red, yellow, and green each turn on once more (but this time in the reverse         order as when the temperature increased).
  - The resistance of the resistor that was used was calculated using ohm's law. KVL, kirchoff's voltage law (which states that the sum of all voltage          drops and rises around a closed loop is 0), was disregarded as a "worst-case" scenario was assumed so as to best protect the arduino from burning       up. It could be the case that the diode is burned up, in which case the voltage drop across the diode is 0. This would mean the voltage drop across the resistor is 5 volts. The maximum current that a GPIO             (general purpose input output) pin on an arduino r3 can supply is 20mA-40mA, which is 0.02A-0.04A (to calculate the resistance you use Amps rather      than milliamps). The lower end was assumed for maximum safety, so R=V/I. V=5 volts; I=0.02 amperes. This gives a resistance of 220 ohms. 

4. Thermistor
  - The thermistor indirectly reads the temperature through the Steinhart-hart equation (bear in mind that we must calculate the resistance of      the thermistor to see how the temperature varies--as a thermistor is a resistor whose resistance changes depending on the temperature).         The steinhart-hart equation is applicable here as an NTC (negative temperature coefficient) thermistor is being used here, which means as       the surrounding temperature increases the resistance of the thermistor decreases exponentially.
  - There's a voltage divider between the thermistor and a 10k Ohm resistor. A voltage divider is a circuit that uses two resistors in series       to produce some output voltage that is split between the two resistors (such that voltage 1 + voltage 2 = input voltage, which is 5V here).
  - When two resistors are in series the current remains the same throughout both resistors, which makes these calculations much easier. The        voltage and resistance, however, will vary (with the exception of the constant resistance of the 10k ohm resistor).
  - The equations can be thought of as two systems of equations using ohm's law (V=IR). In order to make the thermistor read temperature, I         derived the necessary equations (including the steinhart-hart equation). Here's how I did it:
  - Vthermistor=(I)(Rthermistor)
  - I remains the same across both--so we can get I using I=(V10kohmresistor)/(10000)
  - Bear in mind the 10k ohm resistor's voltage is being read by the arduino from the A0 pin using an analogRead and a conversion (which can       easily be found using a point-slope formula with the x-axis being voltage and the y-axis being analogRead and two extreme points (0,0) for      no voltage and (5,1023) for 5 voltages)--the x-axis is voltage as the analogRead depends on the voltage). You then get the relationship         voltage of the 10k ohm resistor=(5/1023)*analogRead(given pin you're reading from).
  - Now we have the current and the voltage of the 10k ohm resistor. We can get the voltage of the thermistor using the equation:
  - Voltagethermistor+Voltage10kohmresistor=5. Then we get: Voltagethermistor=5-Voltage10kohmresistor.
  - Finally we get Rthermistor=Vthermistor/I (from Ohm's Law).

5. 3-W LED
   - For those readers who hate math, this section is far easier than the last section.
   - The 3-W LED, despite being an LED, did NOT require a resistor (the LED has a built-in constant resistor)
   - the + goes to the power rail, which is powered by the battery
   - the - goes to collector on the BJT
   - the S goes to the power rail, which is bowered by the battery
   - PWM is when the voltage averages between extreme voltages of 0 and 5 V to act as if there were some inbetween value, when in reality there     actually isn't. Suppose i=127.5 (50% of the maximum possible value of 255) then the duty cycle (the percentage of time that 5V is sent) is     50%. Half the time the arduino sends 0 volts and the other half the arduino sends 5 volts.
  
6. BJT
   - BJT stands for "bipolar junction transistor", and it is needed in this project as the 3-W LED drives far too much current. A transistor acts as a       switch: whenever the arduino sends a small current to the base a larger current flows from the collector to the emitter, this powers the 3-W LED.
   - The resistance was calculated

## Code 
The code (written in C++) can be found in the repository (also below the README.md) titled "code".

## Images
Images of the breadboard and circuit diagram are also in a repository below the README.md titled "images".
The circuit diagram is in Circuit Diagram.pdf
The pictures of the breadboard are in IMG_2788.jpeg and IMG.2789.jpeg.
