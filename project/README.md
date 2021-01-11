
# Project Title: Boite aux lettres connectée

![maxresdefault](https://user-images.githubusercontent.com/65724356/104201650-44872800-542a-11eb-8d5d-7a5d784a1a1c.jpg)

**Description:**  

Développer une boîte aux lettres connectée capable d’envoyer une notification à chaque fois qu’un nouveau courrier est déposé dans la boîte aux lettres via un système de push notification comme PushOver ou Telegram et également envoyer un email sur une boîte aux lettres Gmail afin d'être ultérieurement traité sur le cloud google via google script.
La notification et l’email doivent inclure une photo de l'intérieur de la boîte aux lettres.

*Alimentation:*

Pour alimenter le système nous avons deux solutions:
La premiere la plus simple est d’alimenter le système par une alimentation murale USB 5v. Avec cela le système pourra être allumé en permanence et ne nécessitera pas de recharge.
La deuxième solution avec un power bank usb 5v elle nécessite une gestion de la batterie par le microcontrôleur afin de déclencher une notification quand la batterie atteint un niveau bas. 

 - [x] **reading from sensors** 
 - [x] **processing raw data**
 - [x] **Information Transmission**
 - [x] **IoT Platform**
 - [x] **Doing an action**

bla bla bla bla bla bla bla bla blabla bla blabla bla blabla bla bla  bla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla blabla bla bla

[Team Report](doc/report.pdf) 

[Team Presentation](doc/presentation.pdf)

# Working Video

 [![Example Video of the porject](https://img.youtube.com/vi/ucZl6vQ_8Uo/0.jpg)](https://www.youtube.com/watch?v=ucZl6vQ_8Uo)

# Components
- 1 x ESP32 Cam Module
- 1 x FTDI232RL
- 1 x Breadboard
- 1 x USB Breakout board
- 1 x Infrared avoidance sensor
- 1 x Resistance 1/4W 5.6k Ohm
- 1 x Resistance 1/4W 3.3 k Ohm
- 1 x 5 V DC USBPower Bank
- 10 x Dupont Male male


# Schematic

![schema](https://user-images.githubusercontent.com/65724356/104210608-61742900-5433-11eb-88aa-b114a1cb01d4.PNG)


![schema1](https://user-images.githubusercontent.com/65724356/104210724-7bae0700-5433-11eb-9a77-1d01aa404399.PNG)


# Overview on the code
Please provide a high level algorithm of your code. if you need to mention some part of the code you can do it like:
```Arduino
	Serial.println('bla bla'); // This line is used to print sth in the serial port
``` 


