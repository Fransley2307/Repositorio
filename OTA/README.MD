```cpp
#include <Arduino.h> 
#include <ESP8266WiFi.h> 
#include <ArduinoOTA.h>
```
O include são as bibliotecas do arduino ESP8266 e é utilizado para atualizar o OTA


```cpp
const char* ssid = "iPhoneFransley"; 
const char* password = "Fransley.2002";
```
Nesste trecho ele ira configurar a rede de wifi


```cpp
void setup(){
  Serial.begin(9600);
``` 
Inicia a comunicação 

```cpp 
  WiFi.mode(WIFI_STA); 
  WiFi.begin(ssid, password); 
```
Faz a configuração do ESP como um cliente wifi w inicia a conexão usando o login e senha do wifi

  
```cpp
  while (WiFi.waitForConnectResult() != WL_CONNECTED){ 
    Serial.println("Conexão falho! Rebooting.."); 
    WiFi.begin(ssid, password);; 
    delay(5000); 
  } 
``` 
Neste trecho ele tenta realizar a conexão duas vezes e coloca um delay de 5 segundos para realizar a proxima tentativa, se cazo não funcionar aparece a mensagem de erro de conexão.

```cpp
  ArduinoOTA.onStart([] (){
    Serial.println("Start"); 
  });
```
Aqui ele imprime a mensagem START na serial

```cpp
  ArduinoOTA.onEnd([] (){
    Serial.println("\nEnd"); 
  });
```
Imprime a mensagem END na serial.

```cpp
  ArduinoOTA.onProgress([] (unsigned int progress, unsigned int total){ /*executado durante a atualização na serial*/
    Serial.printf("Progress: %u%% \r", (progress / (total / 100))); /*Imprime o progresso da atuaização*/
  });
```  
  Primeira parte do codigo ele é executado durante a atualização na serial e depois imprime o progresso dela
  

  
```cpp
  ArduinoOTA.onError([] (ota_error_t error){ /*Vai ser utilizado quando tiver algum erro durante a atualização da OTA*/
      Serial.printf("Error [%u]: ", error); /*Imprime o codigo de ERRO*/
      if (error == OTA_AUTH_ERROR) Serial.println("Auth Failed"); /*Irá imprimir a mensagem de erro na parte do codigo onde apresentar o erro*/
      else if (error == OTA_BEGIN_ERROR) Serial.println("Begin Failed"); /*Vai verificar se o erro foi na atualização da OTA e imprime Begin ERROR*/
      else if (error == OTA_CONNECT_ERROR) Serial.println("Connect Failed"); /*Verifica se o erro foi na conexão para fazer a atualização e imprime OTA_CONNECT_ERROR*/
      else if (error == OTA_RECEIVE_ERROR) Serial.println("Receive Failed"); /*Verifica se o erro foi ao receber os dados da atualização e imprime Recive Failed*/
      
  });
```
Em caso de erro durante a atualização OTA, imprime o código e a mensagem de erro correspondente, indicando se o problema ocorreu no início Begin ERROR, na conexão OTA_CONNECT_ERROR ou no recebimento dos dados Recive Failed.


```cpp
  ArduinoOTA.begin(); /*Vai iniciar o serviço ArduinoOTA*/
  Serial.println("Pronto"); /*Imprime Pronto na serial*/
  Serial.print("IP: "); /*Vai imprimir IP na serial*/
  Serial.println(WiFi.localIP()); /*Imprime o endereço IP local do ESP na serial*/

}
```
Depois de iniciar o serviço ArduinoOTA, imprime-se "Pronto" e o endereço IP local do ESP na serial.

```cpp
void loop(){        
  ArduinoOTA.handle();
}
```
o codigo vai permitir atualizar o firmware do ESP8266 à distancia, não precisando usar o cabo (fisio)
