# ClimatizadorInteligente

// Programa : Display LCD 16 x 2, sensor de temperatura LM35, Resistor de 300 Ohm, Potenciômetro, Cabos Diversos
// Autor : Amanda, Evair, Lucas, Stiven

#include <LiquidCrystal.h>

// Pino analogico para ligacao do LM35
int pin = 0; 

// Variaveis que armazenam a temperatura em Celsius e Fahrenheit
int tempc = 0,tempf=0; 

// Array para precisão na medição
int samples[8]; 

// Variáveis que guardam a temperatura máxima e mínima
int maxtemp = -100,mintemp = 100; 

int i;

int led1=9;

int led2=8;

int led3=7;

//Define os pinos que serão ligados ao LCD
LiquidCrystal lcd(12, 11, 5, 4, 3, 2); 

//Array que desenha o simbolo de grau
byte a[8]= {B00110,B01001,B00110,B00000,B00000,B00000,B00000,B00000,}; 

void setup()
{
  Serial.begin(9600); // Inicializa comunicação serial
  pinMode(12, OUTPUT);
  pinMode(11, OUTPUT);
  lcd.begin(16, 2); //Inicializa 
  lcd.print("Temperatura: ");
  //Atribui a "1" o valor do array "A", que desenha o simbolo de grau
  lcd.createChar(1, a); 
  lcd.setCursor(7,1); //Coloca o cursor na coluna 7, linha 1
  lcd.write(1); //Escreve o simbolo de grau
  lcd.setCursor(15,0);
  lcd.write(1);
  lcd.setCursor(15,1);
  lcd.write(1);
  pinMode(led1,OUTPUT);
  pinMode(led2,OUTPUT);
  pinMode(led3,OUTPUT);
}

void loop()
{
  // Loop que faz a leitura da temperatura 8 vezes
  for(i = 0;i<=7;i++)
  {
    samples[i] = ( 5.0 * analogRead(pin) * 100.0) / 1024.0;
    //A cada leitura, incrementa o valor da variavel tempc
    tempc = tempc + samples[i]; 
    delay(100);
  }

// Divide a variavel tempc por 8, para obter precisão na medição
tempc = tempc/8.0; 

//Converte a temperatura em Fahrenheit e armazena na variável tempf
tempf = (tempc * 9)/ 5 + 32; 

//Armazena a temperatura máxima na variável maxtemp
if(tempc > maxtemp) {maxtemp = tempc;} 

//Armazena a temperatura minima na variavel mintemp
if(tempc < mintemp) {mintemp = tempc;} 


//As linhas abaixo escrevem o valor da temperatura na saída serial
    Serial.print(tempc,DEC);
    Serial.print(" Celsius, ");
    Serial.print(tempf,DEC);
    Serial.print(" fahrenheit -> ");
    Serial.print(maxtemp,DEC);
    Serial.print(" Max, ");
    Serial.print(mintemp,DEC);
    Serial.println(" Min");

delay(100); 
lcd.setCursor(13, 0);
lcd.print(tempc,DEC); //Escreve no display o valor da temperatura
lcd.setCursor(0, 1);
lcd.print("Min:"); 
lcd.setCursor(5,1);  //Posiciona o cursor na coluna 5, linha 1 do display
lcd.print(mintemp,DEC); //Escreve no display o valor da temperatura minima
lcd.setCursor(9, 1); //Posiciona o cursor na coluna 9, linha 1 do display
lcd.print("Max: ");  //Escreve no display o valor da temperatura maxima
lcd.setCursor(13,1);
lcd.print(maxtemp,DEC);

if(tempc<18)

{

digitalWrite(led1,HIGH);

digitalWrite(led2,LOW);

digitalWrite(led3,LOW);

}

if((tempc>=18) && (tempc<27))

{

digitalWrite(led1,LOW);

digitalWrite(led2,HIGH);

digitalWrite(led3,LOW);

}

if((tempc>=27) )

{

digitalWrite(led1,LOW);

digitalWrite(led2,LOW);

digitalWrite(led3,HIGH);

delay(200);
delay(200);

}

tempc=0;

delay(500);
}
