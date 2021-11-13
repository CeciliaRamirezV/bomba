# bomba
#include <MsTimer2.h>
int conta = 0;
int conta2 = 0;
void temporizador(){
  conta ++;
  conta2++;
}


void setup() {
MsTimer2:: set (1000, temporizador);
MsTimer2:: start();
  // inicialización de entradas y salidas
pinMode(3,INPUT);
pinMode(2,INPUT);
pinMode(6,INPUT);
pinMode(7,INPUT);
pinMode(4,OUTPUT);
pinMode(5,OUTPUT);
pinMode(8,OUTPUT);
pinMode(9, OUTPUT);
digitalWrite(4, LOW);
digitalWrite(5, LOW);
digitalWrite(8, LOW);
digitalWrite(9, LOW);
}

void loop() {
  // adquisición
bool Nivel_Bajo  = digitalRead(2);
bool Nivel_Alto = digitalRead(3);
bool Reset_boton = digitalRead(6);
bool DPA = digitalRead(7);
bool Bomba,TONE, TONS,TONE2, TONS2, Q,Q2, Q3, Q4;
  // Control de bomba
bool Reset_frio = 1;  
static bool QP = 0;
Q = !(Nivel_Alto || QP); 
QP = !(!Nivel_Bajo || Q);

// control de falla de bomba

 bool FALLA = (!Nivel_Bajo && Nivel_Alto);
static bool falla_sensor_sucio = 0;

//RESET DE SISTEMA

Reset_frio = 0;
bool Reset_sistema = (Reset_frio || Reset_boton);
Q2 = !(FALLA || falla_sensor_sucio); 
falla_sensor_sucio = !(Reset_sistema || Q2);
// DETECTOR DE PASO DE AGUA
Bomba = (Q2 && Q );
TONE = (Bomba &&!DPA);
 if (TONE)
 {
if  (conta >= 2)
  TONS = true;
  else
  TONS = false;
 }
 else 
 {
  conta= 0;
  TONS = false;
 }
 if (Reset_sistema)
 conta = 0;
 static bool falla_paso_agua = 0;
 Q3 = !(falla_paso_agua || TONS);
 falla_paso_agua = !(Reset_sistema || Q3);

 TONE2 = Bomba;

 if (TONE2)
 {
  if (conta2 >=50)
 { 
  TONS2 = true;
  falla_paso_agua= 0;
 }
 }
 else 
 {
  conta2= 0;
  TONS2 = false;
 }
 if (Reset_sistema)
 conta2 = 0;
 static bool falla_exeso_llenado = 0;
 Q4 = !(falla_exeso_llenado || TONS2);
 falla_exeso_llenado = !(Reset_sistema || Q4);
Bomba = (Q2 && Q  && Q3 && Q4);

// señalización
 digitalWrite(4, Bomba); // BOMBA ENCENDIDA
 digitalWrite(5, falla_sensor_sucio); // SEÑALIZACIÓN DE FALLA DE SENSOR SUCIO
 digitalWrite(8, falla_paso_agua); //
 digitalWrite(9, falla_exeso_llenado);
}
