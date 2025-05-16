# cp2-edgeComputing-Arduino

### INTEGRANTES
Alexandre Lucas - RM: 561732
Murilo Pina - RM 563397
Diego Bondezan - RM: 562013
Vitor Carvalho - RM: 562298
Felipe Otávio - RM: 563521

### NOSSO PROJETO
Com o objetivo de contribuir para a digitalização da vinheria e garantir que a experiência do cliente na loja virtual mantenha o mesmo nível de excelência da loja física, eu e minha equipe desenvolvemos um projeto utilizando a plataforma Arduino. O sistema foi projetado para monitorar, em tempo real, três variáveis fundamentais para a conservação dos vinhos: temperatura, umidade e luminosidade da adega.
Por meio de sensores conectados ao Arduino(DHT11, LDR, que são exibidos no LCD L2C), os dados são coletados continuamente e podem ser integrados ao portal de e-commerce, permitindo que os clientes tenham acesso às condições ambientais em que os vinhos estão armazenados.


### CÓDIGO DO PROJETO NO ARDUINO UNO 6

#include <Adafruit_Sensor.h>                       // Biblioteca DHT Sensor Adafruit 
#include <DHT.h>
#include <DHT_U.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// DHT11
#define DHTPIN 8
#define DHTTYPE DHT11  // Alterado para DHT11
DHT_Unified dht(DHTPIN, DHTTYPE);   

LiquidCrystal_I2C lcd(0x27, 16, 2);

#define LED_VERDE    2
#define LED_AMARELO  3
#define LED_VERMELHO 4
#define BUZZER       5

#define LDR_PIN A0



void setup() {
  dht.begin();
  lcd.init();        
  lcd.backlight();    // Liga o backlight do LCD

  pinMode(LED_VERDE, OUTPUT);
  pinMode(LED_AMARELO, OUTPUT);
  pinMode(LED_VERMELHO, OUTPUT);
  pinMode(BUZZER, OUTPUT);

  lcd.setCursor(0, 0);
  lcd.print("Monitor Vinheria");
  delay(2000);  // Aguarda 2 segundos
  lcd.clear();
}

void loop() {

  sensors_event_t event;                        // inicializa o evento da Temperatura
  dht.temperature().getEvent(&event); 
  float temp = event.temperature;
  dht.humidity().getEvent(&event);  
  float umid = event.relative_humidity;
  
  
  int luz = analogRead(LDR_PIN);      // Lê o valor da luz (LDR)

  // Verifica se houve erro na leitura
  if (isnan(temp) || isnan(umid)) {
    lcd.setCursor(0, 0);
    lcd.print("Erro no DHT11");  // Alterado para DHT11
    delay(2000);
    return;
  }

  lcd.clear();

  // Exibe a temperatura e a umidade
  lcd.setCursor(0, 0);
  lcd.print("T:");
  lcd.print(temp, 1);  // Exibe a temperatura com 1 casa decimal
  lcd.print("C U:");
  lcd.print((int)umid);  // Exibe a umidade como inteiro
  lcd.print("%");

  // Verifica se a temperatura está na faixa normal
  if (temp >= 10 && temp <= 30) {  // Faixa de temperatura do DHT11
    lcd.setCursor(0, 1);
    lcd.print("Temperatura OK ");
    delay(2000);
  } else {
    lcd.setCursor(0, 1);
    lcd.print("TEMP CRITICA!  ");
    digitalWrite(BUZZER, HIGH);
    delay(500);
    digitalWrite(BUZZER, LOW);
    delay(500);
  }

  // Verifica a luminosidade
  if (luz < 300) {
    digitalWrite(LED_VERDE, LOW);
    digitalWrite(LED_AMARELO, LOW);
    digitalWrite(LED_VERMELHO, HIGH);
    digitalWrite(BUZZER, HIGH);
    delay(2000);
    digitalWrite(BUZZER, LOW);
    delay(2000);

    lcd.setCursor(0, 1);
    lcd.print("Amb. muito claro ");
    delay(2000);
  }
  else if (luz >= 300 && luz < 700) {
    digitalWrite(LED_VERDE, LOW);
    digitalWrite(LED_AMARELO, HIGH);
    digitalWrite(LED_VERMELHO, LOW);
    digitalWrite(BUZZER, LOW);

    lcd.setCursor(0, 1);
    lcd.print("Meia luz        ");
    delay(2000);
  }
  else {
    digitalWrite(LED_VERDE, HIGH);
    digitalWrite(LED_AMARELO, LOW);
    digitalWrite(LED_VERMELHO, LOW);
    digitalWrite(BUZZER, LOW); 

    lcd.setCursor(0, 1);
    lcd.print("Amb. escuro     ");
    delay(2000);
  }

  // Verifica a umidade e mostra a situação no LCD
  if (umid >= 50 && umid <= 70) {
    lcd.setCursor(0, 1);
    lcd.print("Umidade OK           ");
    delay(2000);
  } else {
    lcd.setCursor(0, 1);
    lcd.print("Umidade CRITICA!     ");
    digitalWrite(LED_VERMELHO, HIGH);
    delay(2000);
    digitalWrite(BUZZER, HIGH);
    delay(2000);
    digitalWrite(BUZZER, LOW);
    delay(2000);
  }
}

O código basicamente faz com que ao iniciar o programa, ele configura o sensor DHT11, o display LCD, LEDs e o buzzer. No loop principal, o Arduino realiza leituras de temperatura, umidade e luminosidade. Os valores são exibidos no LCD, e o sistema verifica se estão dentro de faixas ideais. Se a temperatura ou umidade estiverem críticas, o buzzer é ativado como alerta. A luminosidade é monitorada por um LDR, e o nível de luz ambiente aciona LEDs (verde, amarelo ou vermelho), além de emitir alertas sonoros se estiver muito clara. 

### LINK DE SIMULAÇÃO DO WOKWI

https://wokwi.com/projects/430970372298218497

