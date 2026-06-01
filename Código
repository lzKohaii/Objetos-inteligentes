import network
import time
import ujson
import onewire
import ds18x20

from machine import Pin, ADC, PWM
from umqtt.simple import MQTTClient

# =========================
# CONFIGURAÇÕES MQTT
# =========================

MQTT_CLIENT_ID = "agua-monitoramento-junior"
MQTT_BROKER = "broker.mqttdashboard.com"

MQTT_TOPIC_SENSOR = "junior/agua/sensores"
MQTT_TOPIC_COMANDO = "junior/agua/comando"
MQTT_TOPIC_STATUS = "junior/agua/status"

# =========================
# DS18B20
# =========================

ds_pin = Pin(15)

ds_sensor = ds18x20.DS18X20(
    onewire.OneWire(ds_pin)
)

roms = ds_sensor.scan()

# =========================
# SENSOR DE TURBIDEZ
# (potenciômetro)
# =========================

turbidez_adc = ADC(Pin(34))
turbidez_adc.atten(ADC.ATTN_11DB)

# =========================
# ATUADORES
# =========================

led = Pin(2, Pin.OUT)

buzzer = PWM(Pin(4))
buzzer.duty(0)

# =========================
# LIMITES
# =========================

LIMITE_TEMP = 30
LIMITE_TURBIDEZ = 50

# =========================
# FUNÇÕES
# =========================

def ligar_alerta():
    led.on()

    buzzer.freq(1000)
    buzzer.duty(512)


def desligar_alerta():
    led.off()

    buzzer.duty(0)


def ler_temperatura():

    if len(roms) == 0:
        return None

    ds_sensor.convert_temp()

    time.sleep_ms(750)

    return ds_sensor.read_temp(roms[0])


def ler_turbidez():

    valor_adc = turbidez_adc.read()

    percentual = int(
        (valor_adc / 4095) * 100
    )

    return percentual


def mqtt_callback(topic, msg):

    print("Mensagem recebida:")
    print(topic, msg)

    if topic == MQTT_TOPIC_COMANDO.encode():

        if msg == b"ON":

            ligar_alerta()

            client.publish(
                MQTT_TOPIC_STATUS,
                "Alerta ligado manualmente"
            )

        elif msg == b"OFF":

            desligar_alerta()

            client.publish(
                MQTT_TOPIC_STATUS,
                "Alerta desligado manualmente"
            )


# =========================
# WIFI
# =========================

print("Conectando ao WiFi", end="")

wifi = network.WLAN(network.STA_IF)

wifi.active(True)

wifi.connect(
    "Wokwi-GUEST",
    ""
)

while not wifi.isconnected():

    print(".", end="")

    time.sleep(0.1)

print("\nWiFi conectado!")

# =========================
# MQTT
# =========================

print("Conectando ao MQTT...")

client = MQTTClient(
    MQTT_CLIENT_ID,
    MQTT_BROKER
)

client.set_callback(mqtt_callback)

client.connect()

client.subscribe(
    MQTT_TOPIC_COMANDO
)

print("MQTT conectado!")

print("DS18B20 encontrados:")

print(roms)

# =========================
# LOOP PRINCIPAL
# =========================

while True:

    client.check_msg()

    temperatura = ler_temperatura()

    turbidez = ler_turbidez()

    if temperatura is None:

        status = "ERRO_SENSOR"

        desligar_alerta()

    elif (
        temperatura >= LIMITE_TEMP
        or
        turbidez >= LIMITE_TURBIDEZ
    ):

        status = "ALERTA"

        ligar_alerta()

    else:

        status = "NORMAL"

        desligar_alerta()

    payload = ujson.dumps({

        "temperatura": temperatura,

        "turbidez": turbidez,

        "status": status

    })

    print(payload)

    client.publish(
        MQTT_TOPIC_SENSOR,
        payload
    )

    time.sleep(2)
