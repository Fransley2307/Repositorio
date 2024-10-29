#include <Arduino.h> /Biblioteca padrão do Arduino/
#include <ESP8266WiFi.h> /Biblioteca para conectar o WIFI do ESP8266/
#include <ArduinoOTA.h> /Biblioteca para atualizar o OTA/

const char* ssid = "iPhoneFransley"; /*Ele ira configurar as redes do wifi */
const char* password = "Fransley.2002";

void setup(){
  Serial.begin(9600); /inicia a comunicação/

  WiFi.mode(WIFI_STA); /Configura o ESP como um cliente WIFI/
  WiFi.begin(ssid, password); /aqui inicia a conexão WIFI com login e senha fornecidos antes/

  while (WiFi.waitForConnectResult() != WL_CONNECTED){ /Tenta fazer a conexão/
    Serial.println("Conexão falho! Rebooting.."); /Mensagem de erro/
    WiFi.begin(ssid, password);; /Tenta novamente fazer a conexão/
    delay(5000); /* Coloca um delay de 5 segundos para fazer a proxima tentativa*/
  }  

  ArduinoOTA.onStart([] (){
    Serial.println("Start"); /Imprime a mensagem START na serial/
  });

  ArduinoOTA.onEnd([] (){
    Serial.println("\nEnd"); /Imprime a mensagem END na serial/
  });

  ArduinoOTA.onProgress([] (unsigned int progress, unsigned int total){ /executado durante a atualização na serial/
    Serial.printf("Progress: %u%% \r", (progress / (total / 100))); /Imprime o progresso da atuaização/
  });

  ArduinoOTA.onError([] (ota_error_t error){ /Vai ser utilizado quando tiver algum erro durante a atualização da OTA/
      Serial.printf("Error [%u]: ", error); /Imprime o codigo de ERRO/
      if (error == OTA_AUTH_ERROR) Serial.println("Auth Failed"); /Irá imprimir a mensagem de erro na parte do codigo onde apresentar o erro/
      else if (error == OTA_BEGIN_ERROR) Serial.println("Begin Failed"); /Vai verificar se o erro foi na atualização da OTA e imprime Begin ERROR/
      else if (error == OTA_CONNECT_ERROR) Serial.println("Connect Failed"); /Verifica se o erro foi na conexão para fazer a atualização e imprime OTA_CONNECT_ERROR/
      else if (error == OTA_RECEIVE_ERROR) Serial.println("Receive Failed"); /Verifica se o erro foi ao receber os dados da atualização e imprime Recive Failed/
      
    
  });
  ArduinoOTA.begin(); /Vai iniciar o serviço ArduinoOTA/
  Serial.println("Pronto"); /Imprime Pronto na serial/
  Serial.print("IP: "); /Vai imprimir IP na serial/
  Serial.println(WiFi.localIP()); /Imprime o endereço IP local do ESP na serial/

}

void loop(){        /o codigo vai permitir atualizar o firmware do ESP8266 à distancia, não precisando usar o cabo (fisio)/
  ArduinoOTA.handle();
}

PLATFORMIO.INI

; PlatformIO Project Configuration File
;
;   Build options: build flags, source filter
;   Upload options: custom upload port, speed and extra flags
;   Library options: dependencies, extra library storages
;   Advanced options: extra scripting
;
; Please visit documentation for the other options and examples
; https://docs.platformio.org/page/projectconf.html

[env:nodemcuv2] ; define um ambiente permitindo configurar varias builds para o mesmo projeto usando o nodemcuv2
platform = espressif8266 ; especifica que o projeto é para um microcontrolador com shipset ESP8266
board = nodemcuv2 ; indica qaul placa de desenvolvimento está sendo usada, a nomenclatura nodemcuv2 refere-se ao módulo NodeMCU v2
framework = arduino ; indica que o projeto usará a framework Arduino com base no shipset ESP8266