# Robot Edubasica
Se trata de un robot controlado por teléfono móvil con Bluetooth y potencia regulable de motores

## Componentes
Consta de:
* Chasis con dos motores de DC
* Arduino
* Shield Edubasica
* Receptor de Bluetooth JY-MCU

## Software
* IDE Arduino, para la programación de Arduino con Edubasica
*  Arduino bluetooth, app de Android para mandar señales de control mediante bluetooth a Arduino


## Funcionamiento
Con el potenciómetro colocado en Edubasica regulamos la potencia de los dos motores entre 0 y 255, se visualiza todo ello en los leds de EDubasica (se encienden 0, 1, 2 o 3 en función de la potencia escogida).

Los leds conectados a pin 3 y 5 (verde y rojo) en Edubasica además lucén más si hay más luz ambiente y menos si hay menos luz.

A la aplicación del dispositivo móvil le asignamos letras a enviar mediante bluetooth a la botonera de la app. Arduino recepciona por el puerto serie y a través del receptor bluetooth esas letras y en función del valor de las mismas activa funciones de paro, avance, retroceso, izquierda o derecha el robot.

## Código
~~~
int motori8 = 8;
int motori9 = 9;
int pizquierda = 10;
int pderecha =  11;
int motord12 = 12;
int motord13 = 13;
int potencia;
int n;
int tiempo = 500;
float porcentaje = 0.20;
int tiempofuncionamiento=200;
int dato;

void setup() {
  Serial.begin (9600);
  for (n = 8; n < 14; n++) {
    pinMode(n, OUTPUT);
  }
  for (n = 3; n < 6; n++) {
    pinMode(n, OUTPUT);
  }


}

void leepotencia() {

  potencia = int (analogRead(A0) / 4);
  //luz = int (((255.0 - (analogRead(A1)/4))/32.0)*255.0);
luz=map(analogRead(A1),880,1023,255,0);
  if (potencia == 0) {
    analogWrite(3, 0);
    digitalWrite(4, LOW);
    analogWrite(5, 0);
  }

  if (potencia > 0 && potencia <= 85) {
    analogWrite (3, luz);
    digitalWrite(4, LOW);
    analogWrite (5, 0);
  }

  if (potencia > 85 && potencia <= 170) {
    analogWrite (3, luz);
    digitalWrite(4, HIGH);
    analogWrite (5, 0);
  }

  if (potencia > 170) {
    analogWrite(3, luz);
    digitalWrite (4, HIGH);
    analogWrite(5, luz);
  }


}

void atras() {
  leepotencia();
  analogWrite(pizquierda, potencia);
  analogWrite(pderecha, potencia);
  digitalWrite(motori8, HIGH);
  digitalWrite(motori9, LOW);
  digitalWrite(motord12, HIGH);
  digitalWrite(motord13, LOW);
}

void adelante() {
  leepotencia();
  analogWrite(pizquierda, potencia);
  analogWrite(pderecha, potencia);
  digitalWrite(motori8, LOW);
  digitalWrite(motori9, HIGH);
  digitalWrite(motord12, LOW);
  digitalWrite(motord13, HIGH);
}
void para() {
  leepotencia();
  analogWrite(pizquierda, potencia);
  analogWrite(pderecha, potencia);
  digitalWrite(motori8, LOW);
  digitalWrite(motori9, LOW);
  digitalWrite(motord12, LOW);
  digitalWrite(motord13, LOW);

}


void derecha() {
  leepotencia();
  analogWrite(pderecha, (int (potencia * porcentaje)));
  analogWrite(pizquierda, potencia);
  digitalWrite(motori8, LOW);
  digitalWrite(motori9, HIGH);
  digitalWrite(motord12, LOW);
  digitalWrite(motord13, HIGH);

}
void izquierda() {
  leepotencia();
  analogWrite(pderecha, potencia);
  analogWrite(pizquierda, (int (potencia * porcentaje)));
  digitalWrite(motori8, LOW);
  digitalWrite(motori9, HIGH);
  digitalWrite(motord12, LOW);
  digitalWrite(motord13, HIGH);

}
void loop() {
 Serial.println (luz);

 if (Serial.available()>0) {
     dato=Serial.read();

//Serial.println (dato);
  }

if (dato=='A') {

  adelante();
  delay (tiempofuncionamiento);
  dato='P';
  }

if (dato=='F') {

  atras();
  delay (tiempofuncionamiento);
  dato='P';
  }

if (dato=='I') {

  izquierda();
  delay (tiempofuncionamiento);
  dato='P';
  }    

if (dato=='D') {

  derecha();
  delay (tiempofuncionamiento);
  dato='P';
  }

if (dato=='P') {

  para();

  }
}
~~~
