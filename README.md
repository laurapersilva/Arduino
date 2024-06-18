# Arduino

PLANEJAMENTO:  Pensamos em um dispositivo de alarme que apita quando alguém passa e é desarmado com uma senha. 

MONTAGEM: Utilizamos de 4 botões, uma resistência, um sensor de distância ultrassônico, um potenciômetro e um buzzer e os posicionamos na breadboard conectada ao arduíno.

RESULTADOS OBTIDOS: Primeiro, coloca-se a senha que é recebida pelo código. Após passar na frente do sensor, é tocado um alarme que só é desativado quando digita-se a senha ele é parado.

CÓDIGO:
#include <SoftwareSerial.h>
#define b0 2
#define b1 3
#define b2 4
#define b3 5
#define led 8
#define pot A0
#define trig 7
#define echo 6
#define buzzer 13

int senha[4];
double sense = 1.0;

void defSen();

void setup(){
  pinMode(b0, INPUT_PULLUP);
  pinMode(b1, INPUT_PULLUP);
  pinMode(b2, INPUT_PULLUP);
  pinMode(b3, INPUT_PULLUP);
  pinMode(echo, INPUT);
  pinMode(trig, OUTPUT);
  pinMode(led, OUTPUT);
  pinMode(buzzer, OUTPUT);
  Serial.begin(9600);
  defSen();
}

bool allDown(){
  return !(digitalRead(b0)==LOW || digitalRead(b1)==LOW || digitalRead(b2)==LOW || digitalRead(b3)==LOW);
}

int butPress(){
  if (digitalRead(b0)==LOW) return 0;
  else if (digitalRead(b1)==LOW) return 1;
  else if (digitalRead(b2)==LOW) return 2;
  else if (digitalRead(b3)==LOW) return 3;
}

long measure(){
  digitalWrite(trig, LOW);
  delayMicroseconds(2);
  digitalWrite(trig, HIGH);
  delayMicroseconds(5);
  digitalWrite(trig, LOW);
  
  return pulseIn(echo, HIGH);;
}

void defSen(){
  while(allDown());
  senha[0] = butPress();
  delay(1000);
  while(allDown());
  senha[1] = butPress();
  delay(1000);
  while(allDown());
  senha[2] = butPress();
  delay(1000);
  while(allDown());
  senha[3] = butPress();
}

bool lockDown(){
  int i = 0;
  while(i<4){
    while(allDown());
    if(butPress() == senha[i]) i++;
    else i = 0;
    delay(100);
  }
  return true;
}

void loop(){
  sense = 14.62 * analogRead(pot);
  Serial.println(sense);
  Serial.println(measure());
  Serial.println("-----");
  if(measure() < sense){
    tone(buzzer, 1300);
    digitalWrite(led, HIGH);
    while(!lockDown());
    noTone(buzzer);
    digitalWrite(led, LOW);
  }  
}
