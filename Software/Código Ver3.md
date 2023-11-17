/******************************************************************************************************************************************
*Nombre del archivo        : ProyectoTratamientoDMF
*Autores                   : Grupo 11 
*Fecha                     : 17/11/23
*Descripción               : Modula intensidad de vibración y lee patrones de movimiento con un sensor MPU6050.
*******************************************************************************************************************************************/


#include "Wire.h"
#include "I2Cdev.h"
#include "MPU6050_6Axis_MotionApps612.h"

MPU6050 mpu68(0x68);

uint8_t error_code = 0U;      // devuelve 0 = success, !0 = error

void setup() {
  pinMode(5,OUTPUT);

  Wire.begin();
  Wire.setClock(100000);
  Serial.begin(115200);
  
  // iniciar MPU6050
  Serial.print("{\"key\": \"/log\", \"value\": \"Initializing device 0x68...\", \"level\": \"DEBUG\"}\n");
  mpu68.initialize();
  error_code = mpu68.dmpInitialize();
  
  // identifica errores
  if (error_code == 1U) {
    Serial.print("{\"key\": \"/log\", \"value\": \"device 0x68 initialization failed: initial memory load failed.\", \"level\": \"ERROR\"}\n");
  }
  if (error_code == 2U) {
    Serial.print("{\"key\": \"/log\", \"value\": \"device 0x68 initialization failed: DMP configuration updates failed.\", \"level\": \"ERROR\"}\n");
  }


  // verifica la conexión
  if (!mpu68.testConnection()) {
    Serial.print("{\"key\": \"/log\", \"value\": \"device 0x68 connection failed.\", \"level\": \"ERROR\"}\n"); 
  }

  // offsets del acelerómetro y giroscopio
  mpu68.setXGyroOffset(0);
  mpu68.setYGyroOffset(0);
  mpu68.setZGyroOffset(0);
  mpu68.setXAccelOffset(0);
  mpu68.setYAccelOffset(0);
  mpu68.setZAccelOffset(0);
  

  
  // calibración del MPU6050
  mpu68.CalibrateAccel(6);
  mpu68.CalibrateGyro(6);
  

  // nueva línea en el serial
  Serial.print("\n");
  
  // encender DMP
  Serial.print("{\"key\": \"/log\", \"value\": \"Enabling DMP...\", \"level\": \"DEBUG\"}\n");
  mpu68.setDMPEnabled(true);
  Serial.print("{\"key\": \"/log\", \"value\": \"Device ready.\", \"level\": \"INFO\"}\n");
}

void loop() {
  int potenciometro = analogRead(A1); //lectura del potenciómetro
  int potencia = map(potenciometro,0,1023,0,255); //convertir lectura en rango de numeros del 0 al 255
  analogWrite(5,potencia);  //alimentar micromotores

  uint8_t fifo_buffer68[64]; // almacenamiento temporal tipo FIFO 
  if (!mpu68.dmpGetCurrentFIFOPacket(fifo_buffer68)) {
    return;
  }

  
 
  Quaternion q68;           // [w, x, y, z]         quaternion 

  mpu68.dmpGetQuaternion(&q68, fifo_buffer68);
  
  //formato JSON para lectura del blender
  //parte movil (hombro)
  Serial.print("{\"key\": \"/joint/0\", \"value\": [");
  Serial.print(q68.w);Serial.print(", ");       //
  Serial.print(q68.x);Serial.print(", ");       //rotación plano XZ horario (+) (traslación horizontal del brazo)
  Serial.print(q68.y);Serial.print(", ");       //rotación plano YZ antihorario (+) (rotación del brazo)
  Serial.print(q68.z);                          //rotación plano XY antihorario (+) (traslación vertical del brazo)
  Serial.print("]}\n");

  //parte inmovil (codo)
  Serial.print("{\"key\": \"/joint/1\", \"value\": [");
  Serial.print("1");Serial.print(", ");
  Serial.print("0");Serial.print(", ");
  Serial.print("0");Serial.print(", ");
  Serial.print("0");
  Serial.print("]}\n");
}
