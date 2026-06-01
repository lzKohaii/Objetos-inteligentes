# Sistema de Monitoramento da Qualidade da Água utilizando ESP32 e MQTT

## Sobre o projeto

Este projeto foi desenvolvido para a disciplina de Objetos Inteligentes da Universidade Presbiteriana Mackenzie.

A proposta foi criar um sistema capaz de monitorar algumas condições relacionadas à qualidade da água utilizando um ESP32, um sensor de temperatura DS18B20 e um sensor de turbidez simulado através de um potenciômetro.

As informações coletadas são enviadas pela internet utilizando o protocolo MQTT. Caso algum valor ultrapasse os limites definidos, o sistema entra em estado de alerta e aciona um LED e um buzzer.

---

## Componentes utilizados

* ESP32
* Sensor de temperatura DS18B20
* Potenciômetro (simulando o sensor de turbidez)
* LED
* Buzzer

---

## Como funciona

O ESP32 realiza a leitura da temperatura e da turbidez em intervalos regulares.

A turbidez foi simulada utilizando um potenciômetro no ambiente Wokwi. Dessa forma foi possível representar diferentes níveis de qualidade da água sem a necessidade de um sensor físico real.

Após realizar as leituras, o sistema verifica se os valores estão dentro dos limites estabelecidos.

### Condição normal

* Temperatura abaixo de 30°C
* Turbidez abaixo de 50%

Nessa situação o LED permanece apagado e o buzzer não é acionado.

### Condição de alerta

* Temperatura igual ou superior a 30°C
* Ou turbidez igual ou superior a 50%

Quando uma dessas condições é atingida, o sistema muda o status para ALERTA, acende o LED e ativa o buzzer.

---

## Comunicação MQTT

Foi utilizado o broker público:

broker.mqttdashboard.com

Tópicos utilizados no projeto:

**Publicação dos sensores**

```text
junior/agua/sensores
```

**Recebimento de comandos**

```text
junior/agua/comando
```

**Mensagens de status**

```text
junior/agua/status
```

Exemplo de mensagem enviada pelo sistema:

```json
{
  "temperatura": 21.18,
  "turbidez": 37,
  "status": "NORMAL"
}
```

---

## Arquivos deste repositório

* `main.py` → código desenvolvido em MicroPython
* `Circuito.png` → montagem do circuito
* `MQTT Normal.png` → funcionamento em condição normal
* `MQTT Alerta Temperatura.png` → alerta causado por temperatura elevada
* `MQTT Alerta Turbidez.png` → alerta causado por turbidez elevada

---

## Simulação

O projeto foi desenvolvido e testado utilizando a plataforma Wokwi, permitindo a simulação do ESP32, dos sensores e da comunicação MQTT sem a necessidade de hardware físico.

---

## Vídeo de demonstração

Link do vídeo:
https://youtu.be/KNlvDvcMlrg

---

## Autor

Lázaro Júnior dos Santos

Universidade Presbiteriana Mackenzie
