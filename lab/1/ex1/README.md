

# Exercise 1 
Test your LED by connecting the orange wire to the red bus (VCC). Is it work?

## Schematic 

Yes is worck

![Test Image](https://user-images.githubusercontent.com/65724356/104125702-b98a2d00-5358-11eb-968c-c0e878e499b9.jpg)


## Code

const int led = 6;
 
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
