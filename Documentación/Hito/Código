// Librerias I2C para controlar el mpu6050
#include "I2Cdev.h"
#include "MPU6050.h"
#include "Wire.h"
MPU6050 sensor;

int ax, ay, az;
int gx, gy, gz;

void setup() {
  Serial.begin(57600);    
  Wire.begin();           
  sensor.initialize();    //Iniciando el sensor
  pinMode(5,OUTPUT);
  if (sensor.testConnection()) Serial.println("Sensor iniciado correctamente");
  else Serial.println("Error al iniciar el sensor");
}

void loop() {
  // Leer las aceleraciones y velocidades angulares
  int potenciometro = analogRead(A1);
  int potencia = map(potenciometro, 0, 1023, 0, 255);
  analogWrite(5,potencia);
  sensor.getAcceleration(&ax, &ay, &az);
  sensor.getRotation(&gx, &gy, &gz);
  float ax_m_s2 = ax * (9.81/16384.0);
  float ay_m_s2 = ay * (9.81/16384.0);
  float az_m_s2 = az * (9.81/16384.0);
  float gx_deg_s = gx * (250.0/32768.0);
  float gy_deg_s = gy * (250.0/32768.0);
  float gz_deg_s = gz * (250.0/32768.0);
  //Mostrar las lecturas
  Serial.print("a[x y z](m/s2) g[x y z](deg/s):\t");
  Serial.print(ax_m_s2); Serial.print("\t");
  Serial.print(ay_m_s2); Serial.print("\t");
  Serial.print(az_m_s2); Serial.print("\t");
  Serial.print(gx_deg_s); Serial.print("\t");
  Serial.print(gy_deg_s); Serial.print("\t");
  Serial.println(gz_deg_s);
  delay(100);
}
