# Final

#**El siguiente repositorio tiene por proposito explicar el proyecto final y como trabajan en conjunto los sensore KY-013 y KY-032**


Desarrollar un proyecto capaz de controlar por medio de una interfaz gráfica el estado de los componentes necesarios y así como el control de otros elementos conectados a un sistema de Arduino haciendo uso de sensores e indicadores, de tal manera que mediante la interacción con dicha interfaz y sensores sea posible establecer ese cambio de estado de los componentes a criterio del usuario a través de un enlace Bluetooth en conjunto con la simulación de un entorno de servicio público limitado.


**Materiales**

- Protoboard
- Arduino MEGA/ Aruino UNO
- Modulo Bluetooth/HC06
- Resistores de 220 ohms 
- LED
- Motor DC
- Pantalla LCD
- Módulo análogo de temperatura KY-013
- Buzzer
- Módulo detector de obstáculos KY-032

Fue necesario crear una aplicación básica de manera que no se invirtiera tanto tiempo que pudiera suponer un retraso para el avance de los factores físicos tal como lo es el circuito. El desarrollo de esta aplicación tuvo lugar en el sitio de MIT App Inventor y su objetivo fue controlar un LED y el motor DC. 

[![interfaz.png](https://i.postimg.cc/J4ktTFxj/interfaz.png)](https://postimg.cc/w796M0SB)


Posteriormente el sistema se amplió en cuanto a los componentes, esto considerando los elementos que se habían listado en un principio, es decir, la inclusión de componentes tales como la pantalla LCD, el Buzzer y el sensor detector de obstáculos.

[![componnnete.png](https://i.postimg.cc/XNT97dDc/componnnete.png)](https://postimg.cc/DmPSxJh8)

[![32.png](https://i.postimg.cc/jjd7kXLK/32.png)](https://postimg.cc/WdCzd0Gf)



El sistema funciona de tal forma que al detectar una obstrucción en la entrada, sonará el buzzer a manera de alarma que notifique el ingreso de una persona, a su vez, cada vez que se realice dicha obstrucción aumenta el contador en el display para tener conocimiento de la cantidad de personas que están dentro del establecimiento. Esto se logra gracias al módulo de temperatura KY-013 y los resultados obtenidos. 

**Conexion arduino**

[![paraauidno.png](https://i.postimg.cc/cHxXhwN8/paraauidno.png)](https://postimg.cc/p9SfX9r2)





**Codigo**


´´´´
include <LiquidCrystal.h>


int foco = 51;
int ventilador = 43;
char valor1 = 0;
int contador;
int IR = 47;
int lecturaIR;
int buzz = 31;
int ventiladorValue = 0;

int Thermistor = A8;
int Vo;
float R1 = 10000;
float logR2, R2, T;
float c1 = 0.001129148, c2 = 0.000234125, c3 = 0.0000000876741; 


LiquidCrystal lcd(8, 9, 4, 5, 6, 7);


void setup() 
{
  //===================setup lcd==================
  lcd.begin (16,2);
  pinMode(foco, OUTPUT);   
  pinMode(ventilador, OUTPUT);
  pinMode(IR,INPUT);
  pinMode(buzz,OUTPUT);
  Serial.begin(9600);  
  analogWrite(ventilador,HIGH);
}

void loop()

{

      lecturaIR = digitalRead(IR);
      lcd.setCursor(0,1); //=======mensaje 1======
      lcd.print("Visitantes: ");
      lcd.setCursor(11,1);//======mensaje 2=======
      lcd.print(String(contador));
      lcd.setCursor(0,0);//=====mensaje 3====
      lcd.print("5/31/2021");
      Serial.println(lecturaIR);
 
  if(lecturaIR==HIGH)
  {
    
          Vo = analogRead(Thermistor);
          R2 = R1 * (1023.0 / (float)Vo - 1.0); 
          logR2 = log(R2);
          T = (1.0 / (c1 + c2*logR2 + c3*logR2*logR2*logR2)); //  Kelvin
          T = T - 273.15; // Kelvin a Celcius

        
          Serial.print("Temperatura: "); 
          Serial.print(T);
          Serial.println(" C"); 


          int thermistorRead = analogRead(Thermistor);
          
          analogWrite(ventilador,R2-9600);
          Serial.print("R2:");
          Serial.println(R2-9600);
          Serial.print("Velocidad motor:");
          Serial.print(R2);

    //=====================================================================================
    Serial.print("\n");  
    valor1 = Serial.read();  
    if(valor1 == '1') {digitalWrite(foco, HIGH); Serial.print("Foco encendido");}
    if(valor1 == '0') {digitalWrite(foco, LOW); Serial.print("Foco apagado");}   
    
  }

  if (lecturaIR == LOW) 
    {
      contador ++;
      digitalWrite(buzz,HIGH);
      delay(30);
      digitalWrite(buzz,LOW);
      
      while(lecturaIR == LOW) //Antirebote 
        {
          Serial.print("\n");  
          valor1 = Serial.read(); 
         if(valor1 == '1') {digitalWrite(foco, HIGH); Serial.print("Foco encendido");}
         if(valor1 == '0') {digitalWrite(foco, LOW); Serial.print("Foco apagado");}   

         //========================================Control de ventilador===================
                 
          Vo = analogRead(Thermistor);
          R2 = R1 * (1023.0 / (float)Vo - 1.0); 
          logR2 = log(R2);
          T = (1.0 / (c1 + c2*logR2 + c3*logR2*logR2*logR2)); // Kelvin
          T = T - 273.15; // Kelvin a Celcius
        
        
          Serial.print("Temperatura: "); 
          Serial.print(T);
          Serial.println(" C"); 


          int thermistorRead = analogRead(Thermistor);
          
          analogWrite(ventilador,R2-9600);
          Serial.print("R2:");
          Serial.println(R2-9600);
          Serial.print("Velocidad motor:");
          Serial.print(R2);

         
         Serial.println("ciclo andando");
         lecturaIR = digitalRead(IR );
         
          
        }
        
     }
}

´´´´


