/**********************************************
*Nombre del archivo        : nrf24_receptor
*Autores                   : Grupo 11 (capibaras)
*Fecha                     : 23/11/23
*Descripción               : recibe las lecturas del nrf24_emisor
***********************************************/

#include <SPI.h>
#include <NRFLite.h>

//configuración del nrf24 
const static uint8_t RADIO_ID = 0; 
const static uint8_t PIN_RADIO_CE = 7;
const static uint8_t PIN_RADIO_CSN = 8;

struct RadioPacket {
  uint8_t FromRadioId;
  uint32_t Data;
};

NRFLite _radio;
RadioPacket _radioData;

void setup() {

  Serial.begin(115200);

}

void loop() {

  while (_radio.hasData()) {

    //recibe e imprime los datos

    _radio.readData(&_radioData);
    double x = _radioData.Data;

    //formato JSON para lectura del blender
    //parte movil(hombro)
    Serial.println("{\"key\": \"/joint/0\", \"value\": [");
    Serial.print(x);

//parte inmovil (codo)
    Serial.println("{\"key\": \"/joint/0\", \"value\": [");
    Serial.print(x);
  }

}
