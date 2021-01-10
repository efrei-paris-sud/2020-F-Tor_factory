

# Exercise 2 
Turn on and off an LED in port 4. Is it work?

## Schematic 
![Test Image](photo.png?raw=true)

## Code
const int led = 4;
 
// the setup routine runs once when you press reset:
void setup() {                
  // initialize the digital pin as an output.
  pinMode(led, OUTPUT);     
}
 
// the loop routine runs over and over again forever:
void loop() {
  digitalWrite(led, HIGH);      // turn the LED on (HIGH is the voltage level)
  delay(1000);                  // wait for a second
  digitalWrite(led, LOW);       // turn the LED off by making the voltage LOW
  delay(1000);                  // wait for a second
}
  
## Board Image
![Board](Arduino_LED.gif?raw=true)

To make a gif you can use [ezgif](https://ezgif.com/maker).
## Issues
- bla bla
- bla bla
- bla bla
