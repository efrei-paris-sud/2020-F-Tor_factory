
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


[Team Report](doc/report.pdf) 

[Team Presentation](doc/presentation.pdf)

# Working Video

 [![Video Projet BLC](https://youtu.be/3NNZsnjdIVs)](https://youtu.be/3NNZsnjdIVs)

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
#include "esp_camera.h"               
#include "Arduino.h"
#include "FS.h"                
#include "SD_MMC.h"            
#include "soc/soc.h"           
#include "soc/rtc_cntl_reg.h"  
#include "driver/rtc_io.h"
#include <EEPROM.h>            
#include "SD.h"
#include <WiFi.h>
#include "ESP32_MailClient.h"
#include "SPIFFS.h"
#include "esp_timer.h"
#include "img_converters.h"
#include "driver/rtc_io.h"





char ssid[] = "TP-*********"; 
char password[] = "*******"; 

// Envoyer un email via Gmail

#define emailSenderAccount    "****@gmail.com" 
#define emailSenderPassword   "******"
#define emailRecipient        "********@gmail.com"
#define emailRecipient2       "5v53xu8xqf@pomail.net"
#define smtpServer            "smtp.gmail.com"
#define smtpServerPort         465 //587 //465
#define emailSubject          "Thierry tu a un nouveau courrier notification"
SMTPData smtpData;

// Pushover settings
char pushoversite[] = "api.pushover.net";
char apitoken[] = "aszcfk9s7mi4ij89rvfscbmdggezc4";
char userkey [] = "um7vbt89udh9s1zidrz1gcoax1c9iq";
int length;
WiFiClient client;

// Pin definition for CAMERA_MODEL_AI_THINKER
#define PWDN_GPIO_NUM     32
#define RESET_GPIO_NUM    -1
#define XCLK_GPIO_NUM      0
#define SIOD_GPIO_NUM     26
#define SIOC_GPIO_NUM     27

#define Y9_GPIO_NUM       35
#define Y8_GPIO_NUM       34
#define Y7_GPIO_NUM       39
#define Y6_GPIO_NUM       36
#define Y5_GPIO_NUM       21
#define Y4_GPIO_NUM       19
#define Y3_GPIO_NUM       18
#define Y2_GPIO_NUM        5
#define VSYNC_GPIO_NUM    25
#define HREF_GPIO_NUM     23
#define PCLK_GPIO_NUM     22
#define TIME_TO_SLEEP  180            //time ESP32 will go to sleep (in seconds)
#define uS_TO_S_FACTOR 1000000ULL   //conversion factor for micro seconds to seconds */
int pictureNumber = 0;


void setup() {
  WRITE_PERI_REG(RTC_CNTL_BROWN_OUT_REG, 0); 
  pinMode(4, OUTPUT);
  pinMode(33, OUTPUT);
  pinMode(14, INPUT);
  digitalWrite(4, LOW);
  digitalWrite(33, HIGH);
  Serial.begin(115200);
  //Serial.setDebugOutput(true);
  Serial.println();


  delay(500);
  Serial.println("Bonjour");
  if (!SPIFFS.begin(true)) {
    Serial.println("Une erreur est survenue lors du montage de SPIFFS");
    ESP.restart();
  }
  else {
    Serial.println("SPIFFS monte avec succe");
  }
  SPIFFS.format();
  EEPROM.begin(400);

  camera_config_t config;
  config.ledc_channel = LEDC_CHANNEL_0;
  config.ledc_timer = LEDC_TIMER_0;
  config.pin_d0 = Y2_GPIO_NUM;
  config.pin_d1 = Y3_GPIO_NUM;
  config.pin_d2 = Y4_GPIO_NUM;
  config.pin_d3 = Y5_GPIO_NUM;
  config.pin_d4 = Y6_GPIO_NUM;
  config.pin_d5 = Y7_GPIO_NUM;
  config.pin_d6 = Y8_GPIO_NUM;
  config.pin_d7 = Y9_GPIO_NUM;
  config.pin_xclk = XCLK_GPIO_NUM;
  config.pin_pclk = PCLK_GPIO_NUM;
  config.pin_vsync = VSYNC_GPIO_NUM;
  config.pin_href = HREF_GPIO_NUM;
  config.pin_sscb_sda = SIOD_GPIO_NUM;
  config.pin_sscb_scl = SIOC_GPIO_NUM;
  config.pin_pwdn = PWDN_GPIO_NUM;
  config.pin_reset = RESET_GPIO_NUM;
  config.xclk_freq_hz = 20000000;
  config.pixel_format = PIXFORMAT_JPEG;

  if (psramFound()) {
    config.frame_size = FRAMESIZE_QVGA; // FRAMESIZE_ + QVGA|CIF|VGA|SVGA|XGA|SXGA|UXGA
    config.jpeg_quality = 10;
    config.fb_count = 2;
  } else {
    config.frame_size = FRAMESIZE_SVGA;
    config.jpeg_quality = 12;
    config.fb_count = 1;
  }

  // Initialisation de la camera
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    Serial.printf("Erreur d'initialisation de la camera avec le code 0x%x", err);
    return;
  }


  //************************************** Connection ******************************************************************
  digitalWrite(33, LOW);
  Serial.print("Connectiion");

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print(".");
    delay(200);
  }

  Serial.println();
  Serial.println("WiFi connecte.");
  Serial.println();



}


void loop() {
  if (digitalRead(14)==LOW) {
    takePicture();
    sendNotification();
  }
}

// Callback fonction pour le statut d'envoi du courriel
void sendCallback(SendStatus msg) {
  // affiche le statut actuel
  Serial.println(msg.info());
  if (msg.success()) {
    Serial.println("----------------");
  }
}

byte pushover(char *pushovermessage)
{
  String message = pushovermessage;

  length = 81 + message.length();

  if (client.connect(pushoversite, 80))
  {
    client.println("POST /1/messages.json HTTP/1.1");
    client.println("Host: api.pushover.net");
    client.println("Connection: close\r\nContent-Type: application/x-www-form-urlencoded");
    client.print("Content-Length: ");
    client.print(length);
    client.println("\r\n");;
    client.print("token=");
    client.print(apitoken);
    client.print("&user=");
    client.print(userkey);
    client.print("&message=");
    client.print(message);
    while (client.connected())
    {
      while (client.available())
      {
        char ch = client.read();
        Serial.write(ch);
      }
    }
    client.stop();
  }
}
void takePicture() {

  //****** flash allumé  ***************************************
  digitalWrite(4, HIGH);
  camera_fb_t * fb = NULL;

  // Prise de la photo
  fb = esp_camera_fb_get();
  if (!fb) {
    Serial.println("Echec de la prise de vue par la camera");
    return;
  }
  //****** flash eteint ***************************************
  digitalWrite(4, LOW);
  String path = "/Courrier.jpg";
  File file = SPIFFS.open(path, FILE_WRITE);
  // Chemin de sauvegarde de l'image



  if (!file) {
    Serial.println("Impossible d'ouvrir le fichier en mode ecriture");
  }
  else {
    file.write(fb->buf, fb->len); // payload (image), payload length
    Serial.printf("Fichier sauve avec le chemin suivant: %s\n", path);

  }
  file.close();
  esp_camera_fb_return(fb);
}
void sendNotification() {
  Serial.println("Preparation de l'envois du couriel");
  Serial.println();
  pushover("Vous avez un nouveau courrier!!!");

  //  Email SMTP Serveur , port, compte et mot de passe
  smtpData.setLogin(smtpServer, smtpServerPort, emailSenderAccount, emailSenderPassword);

  // Nome de l'expediteur et du compte
  smtpData.setSender("Nouveau courrier", emailSenderAccount);

  // Priorite du message High, Normal, Low ou 1 a 5 (1 est la plus forte)
  smtpData.setPriority("High");

  // Sujet du message
  smtpData.setSubject(emailSubject);

  // Corp du message en HTML
  smtpData.setMessage("<div style=\"color:#2f4468;\"><h1>Vous avez un nouveau courrier !</h1><p>- Envoye depuis ESP32cam</p></div>", true);


  // Ajout du destinataire
  smtpData.addRecipient(emailRecipient);
  smtpData.addRecipient(emailRecipient2);
  smtpData.setFileStorageType(MailClientStorageType::SPIFFS);
  smtpData.addAttachFile("/Courrier.jpg");

  smtpData.setSendCallback(sendCallback);

  //Envois du courriel
  if (!MailClient.sendMail(smtpData))
    Serial.println("Erreur lors de l'envoi du couriel, " + MailClient.smtpErrorReason());

  //Efface toutes les donnees de l'objet Email pour liberer la memoire
  digitalWrite(33, HIGH);
  smtpData.empty();
}

