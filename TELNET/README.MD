```cpp
#include <ESP8266WiFi.h>
```
É UMA BIBLIOTECA USADA PARA SE CONECTAR EM UMA PLACA  ESP8266 a redes Wi-Fi.
```cpp
#include <ArduinoOTA.h>
```
É UMA BIBLIOTECA ARDUINOOTA.H É USADA PARA ATUALIZAR DIRETAMENTE PELA REDE WIFI

ESTE COMANDO É USADO PARA SE CONECTAR NA SUA REDE WIFI QUE ESTA CONECTADO NO SEU COMPUTADO
```cpp
const char* ssid = "iPhonedeFransley";`
```
É USADO PARA SABER O NOME DA REDE QUE DEVE SE CONECTAR
```cpp
const char* password = "Fransley.2002";
```
É USADO PARA SABER A SENHA QUE VAI SER INSERIDO NA HORA DA CONEXÃO 

```cpp
const int LED_PIN = 5; // D1
```
ESSA CONSTANTE DECLARA COMO LED_PIN QUE ARMAZENA O NUMERO DO PINO AO QUAL O LED ESTA CONECTADO. 
```cpp
WiFiServer server(2323);
```
AQUI ELE ESTÁ DECLARANDO A PORTA QUE ESTÁ SENDO USADA

```cpp
WiFiClient client;
```
Esse objeto é usado para gerenciar conexões de rede, especialmente para estabelecer conexões de cliente TCP, permitindo que o ESP8266 se conecte a servidores na Internet ou em redes locais. 

```cpp
void setup()
```
É A ONDE CONFIGURA OS ELEMENTOS QUE PROGRAMA E QUE PRECISA ANTES DE ENTRAR NO LOOP
```cpp
pinMode(LED_PIN, OUTPUT);
```
Esse comando é essencial para indicar ao microcontrolador como ele deve usar o pino, se como entrada ou saída.
```cpp
Serial.begin(9600);
```
é usada para iniciar a comunicação serial entre o microcontrolador (como Arduino ou ESP8266) e o computador.
```cpp
WiFi.mode(WIFI_STA);
```
É usado para definir o modo de operação do Wi-Fi no ESP8266.
```cpp
WiFi.begin(ssid, password);
```
ESTE COMANDO É USADO PARA SE CONECTAR EM UMA REDE WIFI.
```cpp
while (WiFi.waitForConnectResult() != WL_CONNECTED) {

    Serial.println("Conexão falhou, tentando novamente...");

    WiFi.begin(ssid, password);

    delay(5000);

  }
```
ESTE COMANDO FALA CASO A REDE WIFI NÃO SE CONECTE ELE APARECE A MENSAGEM A CIMA E FICA EM UM LOOP TENTANDO SE CONECTAR NOVAMENTE, E CASO NÃO CONECTE APARECE A MENSAGEM NOVAMENTE  
```cpp
ArduinoOTA.onStart([]() {

    Serial.println("Iniciando atualização OTA...");

});
```
O CODIGO DEVIDNE UMSA FUNÇÃO QUE SERA CHAMANDA QUANDO UMA ATUALIZAÇÃO COMEÇAR
```cpp
ArduinoOTA.onEnd([]() {

    Serial.println("\nAtualização concluída.");

});
```
O CODIGO DEVIDNE UMSA FUNÇÃO QUE SERA CHAMANDA QUANDO UMA ATUALIZAÇÃO TERMINAR
```cpp
ArduinoOTA.onProgress([](unsigned int progress, unsigned int total) {

    Serial.printf("Progresso: %u%%\r", (progress / (total / 100)));

});
```
ESTE CODIGO DEFINE UMA FUNÇÃO QUE EXIBE O PERCENTUAL DO PROCESSO DE ATUALIZAÇÃO.
```cpp
ArduinoOTA.onError([](ota_error_t error) {

    Serial.printf("Erro[%u]: ", error);

    if (error == OTA_AUTH_ERROR) Serial.println("Falha na autenticação");

    else if (error == OTA_BEGIN_ERROR) Serial.println("Falha no início");

    else if (error == OTA_CONNECT_ERROR) Serial.println("Falha na conexão");

    else if (error == OTA_RECEIVE_ERROR) Serial.println("Falha no recebimento");

    else if (error == OTA_END_ERROR) Serial.println("Falha no finalização");

});
```
ESTE CODIGO FAZ UMA FUNÇÃO DE APARECER CASO DER ERRO NA ATUALIZAÇÃO DO SEU CODIGO, MOSTRANDO A ONDE ELE FALHO COMO MOSTRA AS MENSAGENS A CIMA.
```cpp
ArduinoOTA.begin();

  Serial.println("Pronto para atualizações OTA.");

  Serial.print("Endereço IP: ");

  Serial.println(WiFi.localIP());



  server.begin();

  server.setNoDelay(true);

  Serial.println("Servidor Telnet iniciado...");

}
```
ESTE CODIGO INICIA A FUNCÇÃO DE ATUALIZAÇÃO NO ESP8266, IMPRIMINDO O ENDEREÇO IP DO DISPOSITIVO E INICIALIZA O SERVIÇO TELNET PARA PERMITIR A COMUNICAÇÃO COM OUTRO DISPOSITIVO NA REDE. AS MENSAGENS DE DEPURAÇÃO NO MONITOR SERIAL AJUDAM A CONFIRMAR QUE O DISPOSITIVO ESTÁ CONFIGURADO CORRETAMENTE E PRONTO PARA OPERAR

```cpp
void loop() {

  ArduinoOTA.handle();

  if (server.hasClient()) {

    if (!client || !client.connected()) {

      if (client) client.stop();

      client = server.available();

      Serial.println("Cliente conectado via Telnet.");

      client.println("Digite '1' para ligar o LED e '0' para desligar o LED.");

    } else {

      server.available().stop();

    }

  }
```
ESSE CODIGO ELE VERIFICAÇÃO DE ATUALIZAÇÃO, GERENCIAMENTO DE CONEXÃO COM O CLIENTE, E POR FIM APRECENTA COMO SERA USADO PARA LIGAR E DESLIGAR A LAMPADA   


```cpp
if (client && client.connected() && client.available()) {

    char command = client.read();

    if (command == '1') {

      digitalWrite(LED_PIN, LOW); 

      client.println("LED ligado.");

      Serial.println("LED ligado.");

    } else if (command == '0') {

      digitalWrite(LED_PIN, HIGH); 

      client.println("LED desligado.");

      Serial.println("LED desligado.");

    } else {

      client.println("Comando inválido. Digite '1' para ligar o LED e '0' para desligar o LED.");

    }

  }

}
```
O código no bloco if verifica se há um cliente conectado e se há dados disponíveis.
Ele lê um comando enviado pelo cliente:
Se o comando for '1', o LED é ligado e uma mensagem de confirmação é enviada ao cliente e ao monitor serial.
Se o comando for '0', o LED é desligado e uma mensagem de confirmação é enviada.
Se o comando não for reconhecido, uma mensagem de erro é enviada ao cliente.
Essa lógica permite que o usuário controle o LED do ESP8266 através de uma interface Telnet, proporcionando um método simples e eficiente de interação com o dispositivo pela rede.


NOS NÃO CONSEGUIMOS POR QUESTÃO DE ESTA USANDO W11
MAS TENHO CERTEZA QUE VOCÊ CONSEGUE :)
