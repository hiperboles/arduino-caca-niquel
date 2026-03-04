#include <Wire.h>
#include <LiquidCrystal_I2C.h>
 
LiquidCrystal_I2C lcd(0x20,16,2);
LiquidCrystal_I2C lcd2(0x27,20,4);
 
#define botao 10
#define botaoR 11
#define buzzer  9
#define s1 8
bool L_botao = 1;
bool LA_botao = 1;
 
 
bool L_botaoR = 1;
bool LA_botaoR = 1;
 
int tentativas = 3;
int ganhos = 0;
int ganhar = 1;
 
 
//diamante
byte zero_top[8] = {
  B00000,
  B00000,
  B00000,
  B00000,
  B00000,
  B00100,
  B01110,
  B11111
};
 
byte zero_bottom[8] = {
  B11111,
  B01110,
  B00100,
  B00000,
  B00000,
  B00000,
  B00000,
  B00000
};
 
//coração
byte coracao_top[8] = {
  B00000,
  B00000,
  B00000,
  B00000,
  B00000,
  B01010,
  B11111,
  B11111
};
 
byte coracao_bottom[8] = {
  B11111,
  B01110,
  B00100,
  B00000,
  B00000,
  B00000,
  B00000,
  B00000
};
 
//espadas
byte espada_top[8] = {
  B00000,
  B00000,
  B00000,
  B00000,
  B00000,
  B00100,
  B01110,
  B11111
};
 
byte espada_bottom[8] = {
  B11111,
  B00100,
  B00100,
  B00000,
  B00000,
  B00000,
  B00000,
  B00000
};
 
//paus
byte paus_top[8] = {
  B00000,
  B00000,
  B00000,
  B00000,
  B00000,
  B11111,
  B11111,
  B00011
};
 
byte paus_bottom[8] = {
  B00110,
  B01100,
  B11000,
  B00000,
  B00000,
  B00000,
  B00000,
  B00000
};
  
void setup()
{
  //inicialização
  lcd.init();
  lcd.backlight();
  lcd2.init();
  lcd2.backlight();
  
  //iniciando lcd
  lcd.setCursor(0,0);
  lcd.print(F("Iniciando..."));
  lcd2.setCursor(0,0);
  lcd2.print(F("Iniciando..."));
  delay(1000);
  lcd.clear();
  lcd.print(F("Bem vindo!"));
  lcd.setCursor(0,1);
  lcd.print(F("Pressione jogar"));
  
  
  //iniciando lcd2
  lcd2.clear();
  
  
  pinMode(botao, INPUT_PULLUP);
  pinMode(botaoR, INPUT_PULLUP);
  pinMode(2,OUTPUT);
  pinMode(3, OUTPUT);
  pinMode(4, OUTPUT);
  pinMode(5, OUTPUT);
  pinMode(6, OUTPUT);
  pinMode(buzzer, OUTPUT);
  randomSeed(analogRead(A0)); //semente para randomizar sequências
  
  
  
  //criação de caracteres
  lcd.createChar(0, zero_top);
  lcd.createChar(1, zero_bottom);
  lcd.createChar(2, coracao_top);
  lcd.createChar(3, coracao_bottom);
  lcd.createChar(4, espada_top);
  lcd.createChar(5, espada_bottom);
  lcd.createChar(6, paus_top);
  lcd.createChar(7, paus_bottom);
}
 
void loop()
{ 
  int caso1 = digitalRead(s1);
  
  if(caso1==0){
  	lcd.noBacklight();
    lcd2.noBacklight();
  }else if(caso1==1){
  	lcd.backlight();
    lcd2.backlight();
  }
  
  L_botao = digitalRead(botao);
  
  if(!L_botao && LA_botao){
    if(tentativas > 0){
    delay(30);
    lcd.clear();
   	sorteio();
    }else{
    lcd2.clear();
    lcd2.setCursor(0,1);
    lcd2.print(F("Esgotadas!"));
    lcd2.setCursor(0,0);
    lcd2.print(F("Ganhos:"));
    lcd2.setCursor(7,0);
    lcd2.print(ganhos);
    delay(3000);
    lcd2.clear();
    delay(30);
   }
  }
  LA_botao = L_botao;
  
  L_botaoR = digitalRead(botaoR);
  if(!L_botaoR && LA_botaoR){
  	resetar();
    delay(50);
  }
  LA_botaoR = L_botaoR;
  
  atualizarLCD2();
}
 
void sorteio(){
  tentativas--;
  for(int i = 0; i<=4; i++){
    
    int n1=random(0,4) * 2;
    int n2=random(0,4) * 2;
    int n3=random(0,4) * 2;
    
    while(n1==n2){
     n1=random(0,4) * 2;
     n2=random(0,4) * 2;
     n3=random(0,4) * 2;
    }
    
    lcd.clear();
    
    lcd.setCursor(5,0); lcd.write(n1);
	lcd.setCursor(5,1); lcd.write(n1+1);
 
	lcd.setCursor(9,0); lcd.write(n2);
	lcd.setCursor(9,1); lcd.write(n2+1);
 
	lcd.setCursor(13,0); lcd.write(n3);
	lcd.setCursor(13,1); lcd.write(n3+1);
    
    musica();
    
    delay(300);
    
  }
  
  
  int resultado = random(0,3);
  
  if(resultado == 1){
    lcd.clear();
  	int simb = random(0,4)*2;
    
    if(simb != 6){
    lcd.setCursor(5,0); lcd.write(simb);
	lcd.setCursor(5,1); lcd.write(simb+1);
 
	lcd.setCursor(9,0); lcd.write(simb);
	lcd.setCursor(9,1); lcd.write(simb+1);
 
	lcd.setCursor(13,0); lcd.write(simb);
	lcd.setCursor(13,1); lcd.write(simb+1);
    delay(2000);
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print(F("Voce ganhou!"));
    lcd.setCursor(0,1);
    lcd.print(F("+1.0 ganhos"));
    ganhos+=ganhar;
    vitoria();
    delay(150);
    }
    else if(simb == 6){
      roleta();
    }
    
    
    
  }else{
    lcd.clear();
    lcd.setCursor(0,0);
  	lcd.print(F("sem ganhos."));
    derrota();
  }
  
  
  
  
}
 
 
void resetar(){
  lcd.clear();
  lcd2.clear();
  tentativas = 3;
  ganhos = 0;
  
  lcd.setCursor(0,0);
  lcd.print(F("Reiniciando..."));
  lcd2.setCursor(0,0);
  lcd2.print(F("Reiniciando..."));
  delay(2000);
  lcd.clear();
  lcd2.clear();
  
  //reinicio lcd
  lcd.setCursor(0,0);
  lcd.print(F("Bem vindo!"));
  lcd.setCursor(0,1);
  lcd.print(F("Pressione jogar"));
}
 
 
void atualizarLCD2(){
  lcd2.setCursor(0,0);
  lcd2.print(F("Tentativas:"));
  lcd2.setCursor(11,0);
  lcd2.print(tentativas);
  lcd2.setCursor(0,1);
  lcd2.print(F("Ganhos:"));
  lcd2.setCursor(7,1);
  lcd2.print(ganhos);
}
 
void roleta(){
  	randomSeed(analogRead(A0));
  	int simb = 6;
  	
  	lcd.clear();
    lcd.setCursor(5,0); lcd.write(simb);
	lcd.setCursor(5,1); lcd.write(simb+1);
 
	lcd.setCursor(9,0); lcd.write(simb);
	lcd.setCursor(9,1); lcd.write(simb+1);
 
	lcd.setCursor(13,0); lcd.write(simb);
	lcd.setCursor(13,1); lcd.write(simb+1);
    delay(2000);
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print(F("Voce ganhou!"));
    lcd.setCursor(0,1);
    lcd.print(F("+1.0 ganhos x ?"));
  
  
  int multiplicador = random(1,5);
  int mult = multiplicador;
  int led = 2;
  
  do{
  	multiplicador--;
    
    digitalWrite(led,HIGH);
    led++;
    subindo();
    delay(1500);
    
  }while(multiplicador>0);
  
  delay(500);
  ganhos += ganhar * mult;
  
  lcd.clear();
  lcd2.clear();
  
  lcd2.setCursor(0,0);
  lcd2.print("$$$$$$$$$$$$$$$$");
  lcd2.setCursor(0,1);
  lcd2.print("$$$$$$$$$$$$$$$$");
  
  for(int i = 0; i<1;i++){
  lcd.setCursor(0,0);
  lcd.print("$$$$$ x" + String(mult) + ".0 $$$$$");
  lcd.setCursor(0,1);
  lcd.print("$COELHO MALUCO!$");
  coelhoMaluco();
  delay(200);
  }
  
  apagar();
}
 
 
 
void apagar(){
  delay(300);
  digitalWrite(2,LOW);
  digitalWrite(3,LOW);
  digitalWrite(4,LOW);
  digitalWrite(5,LOW);
  digitalWrite(6,LOW);
  lcd2.clear();
}

void musica() {
  tone(buzzer, 900, 120);
  delay(130);
  tone(buzzer, 1100, 120);
  delay(130);
  tone(buzzer, 1300, 120);
  delay(130);
  tone(buzzer, 1500, 120);
  delay(130);
  tone(buzzer, 1700, 120);
  delay(130);
  noTone(buzzer);
}

void derrota() {
  tone(buzzer, 1000, 200);
  delay(220);
  tone(buzzer, 800, 200);
  delay(220);
  tone(buzzer, 600, 250);
  delay(270);
  tone(buzzer, 400, 300);
  delay(320);

  tone(buzzer, 300, 150);
  delay(170);

  noTone(buzzer);
}

void vitoria() {
  tone(buzzer, 1200, 100);
  delay(120);
  tone(buzzer, 1400, 100);
  delay(120);
  tone(buzzer, 1600, 100);
  delay(120);
  tone(buzzer, 1800, 100);
  delay(120);

  tone(buzzer, 2000, 80);
  delay(100);
  tone(buzzer, 2300, 80);
  delay(100);
  tone(buzzer, 2600, 200);
  delay(220);

  tone(buzzer, 2800, 400);
  delay(420);

  noTone(buzzer);
}

void coelhoMaluco() {
   tone(buzzer, 523); delay(100); noTone(buzzer); delay(30); 
  tone(buzzer, 587); delay(100); noTone(buzzer); delay(30); 
  tone(buzzer, 659); delay(100); noTone(buzzer); delay(30); 
  tone(buzzer, 698); delay(100); noTone(buzzer); delay(30); 
  tone(buzzer, 784); delay(120); noTone(buzzer); delay(40); 
  tone(buzzer, 880); delay(130); noTone(buzzer); delay(50); 

  tone(buzzer, 1046); delay(80); noTone(buzzer); delay(20); 
  tone(buzzer, 988); delay(60); noTone(buzzer); delay(20);  
  tone(buzzer, 1174); delay(80); noTone(buzzer); delay(30);

  tone(buzzer, 880); delay(60); noTone(buzzer); delay(10);
  tone(buzzer, 988); delay(60); noTone(buzzer); delay(10);
  tone(buzzer, 1046); delay(60); noTone(buzzer); delay(10);
  tone(buzzer, 880); delay(60); noTone(buzzer); delay(10);

  tone(buzzer, 1397); delay(200); noTone(buzzer); delay(30);
  tone(buzzer, 1568); delay(200); noTone(buzzer); delay(30);
  tone(buzzer, 1760); delay(300); noTone(buzzer);

}

void subindo() {
  tone(buzzer, 880); delay(60); noTone(buzzer); delay(20); 
  tone(buzzer, 988); delay(60); noTone(buzzer); delay(20); 
  tone(buzzer, 1046); delay(60); noTone(buzzer); delay(20); 
  tone(buzzer, 1174); delay(60); noTone(buzzer); delay(20);
  tone(buzzer, 1318); delay(70); noTone(buzzer); delay(30); 
  tone(buzzer, 1568); delay(90); noTone(buzzer); delay(30);           
}






 