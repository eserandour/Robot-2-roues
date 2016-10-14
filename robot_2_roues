/////////////////////////////////////////////////////////////////////////////////////
/*
   Robot à 2 roues télécommandé par IR (version 2016.10.06a)
   Copyright 2016 - Eric Sérandour
   http://chaplab.info

   This program is free software; you can redistribute it and/or modify
   it under the terms of the GNU General Public License as published by
   the Free Software Foundation; either version 2 of the License, or
   (at your option) any later version.

   This program is distributed in the hope that it will be useful,
   but WITHOUT ANY WARRANTY; without even the implied warranty of
   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
   GNU General Public License for more details.

   You should have received a copy of the GNU General Public License along
   with this program; if not, write to the Free Software Foundation, Inc.,
   51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.
*/
//////////////////////////////////////////////////////////////////////////////////////
/*
  Compilation réussie avec Arduino 1.0.5, pas avec Arduino 1.6.12
  Type de carte : Arduino Uno
*/
//////////////////////////////////////////////////////////////////////////////////////

#include <Servo.h>
#include <IRremote.h>  // Téléchargeable ici : https://github.com/z3t0/Arduino-IRremote

#define PIN_SERVO_G   6
#define PIN_SERVO_D   7
#define PIN_IR        8
#define PIN_LED_R    12
#define PIN_LED_J    13
#define PIN_BUZZER    2

// Création de 2 objets Servo pour contrôler chacun des servomoteurs
Servo myServoG;    // Le servomoteur qui supporte la roue gauche
Servo myServoD;    // Le servomoteur qui supporte la roue droite

// Les moteurs des roues
const int VIT_G0 = 1324;  // Le moteur gauche est à l'arrêt (à adapter)
const int VIT_D0 = 1356;  // Le moteur droit est à l'arrêt (à adapter)
const int VIT_MAX = 100;  // Vitesse maximum
int vitG = VIT_G0;        // Vitesse du moteur gauche
int vitD = VIT_D0;        // Vitesse du moteur gauche
int robotBouge = 0;       // 0 : immobile // 1 : en avant // -1 : en arrière       

// La télécommande IR
IRrecv irrecv(PIN_IR);
decode_results results;

///////////////////////////////////////////////////////////////////////////////////////////////////////

void setup()
{
  Serial.begin(9600);
  irrecv.enableIRIn(); // Start the receiver
  
  pinMode(PIN_SERVO_G, OUTPUT);
  pinMode(PIN_SERVO_D, OUTPUT);
  pinMode(PIN_LED_R, OUTPUT);
  pinMode(PIN_LED_J, OUTPUT);
  pinMode(PIN_BUZZER, OUTPUT);
   
  myServoG.attach(PIN_SERVO_G);  
  myServoD.attach(PIN_SERVO_D); 

  // On n'avance pas !!! 
  robotImmobile();
  
  // 3 petites notes pour commencer
  for (int i = 1; i <= 3 ; i++) {
    playNote('c', 100); 
    delay(50); 
  }  
}

///////////////////////////////////////////////////////////////////////////////////////////////////////

void loop() {
  digitalWrite(PIN_LED_J, LOW);
  if (irrecv.decode(&results)) {
    Serial.println(results.value, HEX);
    irrecv.resume();  // Receive the next value
    switch (results.value) {
      case 0x1A1A840D :  // Avance : touche CH up
        digitalWrite(PIN_LED_J, HIGH);
        robotAvance(0, VIT_MAX, 2000); 
        break;
      case 0xBE3789AF :  // Recule : touche CH down
        digitalWrite(PIN_LED_J, HIGH);
        robotRecule(0, VIT_MAX, 2000);
        break;        
      case 0xB4376089 :  // Pivote à gauche : touche VOL down
        digitalWrite(PIN_LED_J, HIGH);
        robotPivoteG(VIT_MAX, 500);
        break;
      case 0xF52A30ED :  // Pivote à droite : touche VOL up
        digitalWrite(PIN_LED_J, HIGH);
        robotPivoteD(VIT_MAX, 500);
        break;
      case 0x9FDBA829 :  // Décélère : touche P/L
        digitalWrite(PIN_LED_J, HIGH);
        if (robotBouge != 0) { robotDecelere(VIT_MAX, 0, 2000); }
        break;   
      case 0x182E178D :  // Immobile : touche PAS DE SON
        digitalWrite(PIN_LED_J, HIGH);
        robotImmobile();
        break;          
      case 0x383952AF :  // Immobile : touche MARCHE / ARRET
        digitalWrite(PIN_LED_J, HIGH);
        robotImmobile();
        break;
    }
  }
  delay(100); 
}

///////////////////////////////////////////////////////////////////////////////////////////////////////
// Le robot est à l'arrêt
///////////////////////////////////////////////////////////////////////////////////////////////////////

void robotImmobile()
 {
  vitG = VIT_G0;
  vitD = VIT_D0; 
  myServoG.writeMicroseconds(vitG);  // Moteur à l'arrêt 
  myServoD.writeMicroseconds(vitD);  // Moteur à l'arrêt  
  robotBouge = 0;  // false
 }
 
///////////////////////////////////////////////////////////////////////////////////////////////////////
// Le robot accélère en avançant
///////////////////////////////////////////////////////////////////////////////////////////////////////

void robotAvance(int vitMin, int vitMax, int duree)
{
  for (int i = vitMin; i <= vitMax; i++){
    vitG = VIT_G0 + i;
    vitD = VIT_D0 - i;    
    myServoG.writeMicroseconds(vitG);
    myServoD.writeMicroseconds(vitD);   
    delay(duree / (vitMax-vitMin));
  }
  robotBouge = 1;  // true
}

///////////////////////////////////////////////////////////////////////////////////////////////////////
// Le robot accélère en reculant
///////////////////////////////////////////////////////////////////////////////////////////////////////

void robotRecule(int vitMin, int vitMax, int duree)
{
  for (int i = vitMin; i <= vitMax; i++){
    vitG = VIT_G0 - i;
    vitD = VIT_D0 + i;    
    myServoG.writeMicroseconds(vitG);
    myServoD.writeMicroseconds(vitD);   
    delay(duree / (vitMax-vitMin));
  }
  robotBouge = -1;  // true
}

///////////////////////////////////////////////////////////////////////////////////////////////////////
// Le robot décélère
///////////////////////////////////////////////////////////////////////////////////////////////////////

void robotDecelere(int vitMax, int vitMin, int duree)
{
  for (int i = vitMax; i >= vitMin; i--){
    vitG = VIT_G0 + (i * robotBouge);
    vitD = VIT_D0 - (i * robotBouge);
    myServoG.writeMicroseconds(vitG);
    myServoD.writeMicroseconds(vitD);
    delay(duree / (vitMax-vitMin));
  }
  robotBouge = 0;  // false
}

///////////////////////////////////////////////////////////////////////////////////////////////////////
// Le robot pivote à gauche
///////////////////////////////////////////////////////////////////////////////////////////////////////

void robotPivoteG(int vit, int duree)
{
  vitG = VIT_G0 - vit;
  vitD = VIT_D0 - vit;
  myServoG.writeMicroseconds(vitG);
  myServoD.writeMicroseconds(vitD);
  delay(duree);
  robotImmobile();
}

///////////////////////////////////////////////////////////////////////////////////////////////////////
// Le robot pivote à droite
///////////////////////////////////////////////////////////////////////////////////////////////////////

void robotPivoteD(int vit, int duree)
{  
  vitG = VIT_G0 + vit;
  vitD = VIT_D0 + vit;
  myServoG.writeMicroseconds(vitG);
  myServoD.writeMicroseconds(vitD);
  delay(duree);
  robotImmobile();
}

///////////////////////////////////////////////////////////////////////////////////////////////////////
// Buzzer
///////////////////////////////////////////////////////////////////////////////////////////////////////

void playTone(int tone, int duration)
{
  for (long i = 0; i < duration * 1000L; i += tone * 2) {
    digitalWrite(PIN_BUZZER, HIGH);
    delayMicroseconds(tone);
    digitalWrite(PIN_BUZZER, LOW);
    delayMicroseconds(tone);
  }
}

void playNote(char note, int duration)
{
  char names[ ] = { 'c', 'd', 'e', 'f', 'g', 'a', 'b', 'C' };        // Do Ré Mi Fa Sol La Si Do
  int tones[ ] = { 1915, 1700, 1519, 1432, 1275, 1136, 1014, 956 };  // Demi-période en ms
  // Play the tone corresponding to the note name
  for (int i = 0; i < 8; i++) {
    if (names[i] == note) {
      playTone(tones[i], duration);
    }
  }
}

///////////////////////////////////////////////////////////////////////////////////////////////////////
