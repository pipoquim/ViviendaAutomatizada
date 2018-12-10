//blynk-ds18b20Demo.ino
//octubre 2018
//rPrim Tech, Barcelona

//Copyright (C) 2018 by jaume Nogués <jnogues at gmail dot com>

#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

#include <OneWire.h>
#include <DallasTemperature.h>
#define ONE_WIRE_BUS 10//GPIO10, potser GPIO9 en algunes nodeMCU
OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature DS18B20(&oneWire);

float temperatura=0;//variable per guardar valor de T


char auth[] = "e84bf513beb54c128c0507c2b2b67e5d";//afegiu el token del vostre projecte!!

char ssid[] = "Robotica2018";
char pass[] = "1123prim2018";
char vpsServer[] = "vps249990.ovh.net";
//IPAddress ipServer (192, 168, 1, 222);//Servidor local
BlynkTimer timer; // Creem un temporitzador anomenat "timer"! 


void setup()
{
  Serial.begin(115200);
  pinMode(16, OUTPUT);
  DS18B20.begin();
  Blynk.begin(auth, ssid, pass,vpsServer,8080);
  timer.setInterval(1000L, intermitaLed16);//configuro una tasca que es repeteix cada 1000ms
  timer.setInterval(15000L, mesuraTemperatura);//configuro una tasca que es repeteix cada 15s
  
}

void loop()
{
  Blynk.run();
  timer.run();
}

void intermitaLed16()//tasca a repetir cada 1000ms
{
  digitalWrite(16, !digitalRead(16));
}

void mesuraTemperatura()//tasca dada 15s

{
  DS18B20.requestTemperatures(); 
  temperatura = DS18B20.getTempCByIndex(0);
  
  Serial.print("T= ");
  Serial.println(temperatura);
  Blynk.virtualWrite(V0,temperatura);
}
