# LI-10-Programas-Wokwi
## 1.- Sensor de temperatura y display LCD:

### Codigo
```
from machine import Pin, I2C
import time
import dht
from lcd_api import LcdApi
from i2c_lcd import I2cLcd

# Configuración del DHT22
dht_pin = Pin(15)
sensor = dht.DHT22(dht_pin)

# Configuración de LCD I2C
i2c = I2C(0, sda=Pin(0), scl=Pin(1), freq=400000)
lcd = I2cLcd(i2c, 0x27, 2, 16)

lcd.putstr("Iniciando.Espere")
time.sleep(2)
lcd.clear()

while True:
    try:
        sensor.measure()
        temp = sensor.temperature()
        hum = sensor.humidity()

        lcd.clear()
        lcd.move_to(0, 0)
        lcd.putstr("Temp: {:.1f} C".format(temp))
        time.sleep(2)
        lcd.move_to(0, 1)
        lcd.putstr("Hum: {:.1f} %".format(hum))

        time.sleep(2)

    except OSError as e:
        lcd.clear()
        lcd.putstr("Error lectura")
        time.sleep(2)
```
### Resultado
https://github.com/user-attachments/assets/9f7cef91-4349-46d2-939f-45fd97487535

## 2.- Semáforo básico:

### Codigo
```
from machine import Pin
from time import sleep

# Definir LEDs
led_rojo = Pin(15, Pin.OUT)
led_amarillo = Pin(14, Pin.OUT)
led_verde = Pin(13, Pin.OUT)

while True:
    # Encender verde
    led_verde.on()
    led_amarillo.off()
    led_rojo.off()
    sleep(5)

    # Encender amarillo
    led_verde.off()
    led_amarillo.on()
    sleep(2)

    # Encender rojo
    led_amarillo.off()
    led_rojo.on()
    sleep(5)
```
### Resultado
https://github.com/user-attachments/assets/3186f89b-9213-40fd-82ca-e23c0d0bc714

## 3.- Alarma simple con botón:

### Codigo
```
from machine import Pin
from time import sleep

# Configurar el buzzer y el botón
buzzer = Pin(12, Pin.OUT)
boton = Pin(10, Pin.IN, Pin.PULL_UP)

while True:
    if boton.value() == 0:  # Si se presiona el botón
        buzzer.on()
        print("¡Alarma activada!")
    else:
        buzzer.off()
        print("Sistema en espera")
    sleep(0.2)
```
### Resultado
https://github.com/user-attachments/assets/27fd9f6f-52ca-486b-91fd-b1ad73e04f8a

## 4.- Monitor de luz ambiental con LED:

### Codigo
```
from machine import Pin, ADC, PWM
from time import sleep

# Configuración del LDR (entrada analógica)
ldr = ADC(26)  # GP26 es ADC0

# Configuración del LED con PWM
led = PWM(Pin(15))
led.freq(1000)  # Frecuencia del PWM

while True:
    valor_luz = ldr.read_u16()  # Lectura de 0 a 65535
    print("Luz detectada:", valor_luz)

    # Invertimos el valor para que más luz = LED más tenue
    brillo = 65535 - valor_luz
    led.duty_u16(brillo)

    sleep(0.2)
```
### Resultado
https://github.com/user-attachments/assets/c3b9c398-59ec-4de2-b6a5-f957b622a0b2

## 5.- Control automático de ventilador con potenciómetro:

### Codigo
```
from machine import ADC, Pin, PWM
from time import sleep

# Potenciómetro en GP26 (ADC0)
pot = ADC(26)

# LED (ventilador simulado) con PWM en GP15
led = PWM(Pin(15))
led.freq(1000)

while True:
    valor = pot.read_u16()  # Rango: 0 - 65535
    print("Velocidad:", valor)
    
    led.duty_u16(valor)  # Simula velocidad del ventilador
    sleep(0.2)
```
### Resultado
https://github.com/user-attachments/assets/6671ad39-5ab3-4450-b6dd-a20ba922fbda

## 6.- Juego simple de "Simón dice" con LEDs y botones:

### Codigo
```
from machine import Pin
from time import sleep
import random

# LEDs
leds = [Pin(2, Pin.OUT), Pin(3, Pin.OUT), Pin(4, Pin.OUT)]

# Botones
botones = [Pin(10, Pin.IN, Pin.PULL_UP), Pin(11, Pin.IN, Pin.PULL_UP), Pin(12, Pin.IN, Pin.PULL_UP)]

def mostrar_secuencia(seq):
    for idx in seq:
        leds[idx].on()
        sleep(0.5)
        leds[idx].off()
        sleep(0.2)

def leer_respuesta(longitud):
    respuesta = []
    while len(respuesta) < longitud:
        for i, boton in enumerate(botones):
            if boton.value() == 0:  # presionado
                respuesta.append(i)
                leds[i].on()
                sleep(0.3)
                leds[i].off()
                # Espera a que suelte el botón para evitar múltiples lecturas
                while boton.value() == 0:
                    pass
        sleep(0.1)
    return respuesta

secuencia = []
nivel = 1

print("¡Juego Simón Dice!")

while True:
    print(f"Nivel {nivel}")
    nuevo_num = random.randint(0, 2)
    secuencia.append(nuevo_num)
    mostrar_secuencia(secuencia)
    
    respuesta = leer_respuesta(len(secuencia))
    
    if respuesta == secuencia:
        print("Correcto!")
        nivel += 1
        sleep(1)
    else:
        print("Incorrecto! Fin del juego.")
        break
```
### Resultado
https://github.com/user-attachments/assets/556fc584-0116-4f70-bcba-0d315b59feb8

## 7.- Contador de personas con sensores ultrasónicos y display 7 segmentos:

### Codigo
```
from machine import Pin
from time import sleep
import tm1637

tm = tm1637.TM1637(clk=Pin(2), dio=Pin(3))

boton_entrada = Pin(10, Pin.IN, Pin.PULL_UP)
boton_salida = Pin(11, Pin.IN, Pin.PULL_UP)

contador = 0
tm.number(contador)

while True:
    if boton_entrada.value() == 0:
        if contador < 9999:
            contador += 1
            tm.number(contador)
            print("Entrada detectada. Conteo:", contador)
            sleep(0.5)
        while boton_entrada.value() == 0:
            pass

    if boton_salida.value() == 0:
        if contador > 0:
            contador -= 1
            tm.number(contador)
            print("Salida detectada. Conteo:", contador)
            sleep(0.5)
        while boton_salida.value() == 0:
            pass

    sleep(0.1)
```
### Resultado
https://github.com/user-attachments/assets/1ca3868a-151c-4020-a546-7f76e6b00fa9

## 8.- Detector de gas con alarma LED y buzzer:

### Codigo
```
from machine import Pin
from time import sleep

# Sensor de gas conectado a GP14 (salida digital D0)
gas_sensor = Pin(14, Pin.IN)

# LED y buzzer como alerta
led = Pin(15, Pin.OUT)
buzzer = Pin(16, Pin.OUT)

while True:
    if gas_sensor.value() == 0:
        print("¡Gas detectado!")
        led.value(1)
        buzzer.value(1)
    else:
        print("Sin gas.")
        led.value(0)
        buzzer.value(0)
    sleep(1)
```
### Resultado
https://github.com/user-attachments/assets/64583862-8284-4629-8cf8-421f2742c961

## 9.- Control de acceso con contraseña usando teclado matricial y LCD:

### Codigo
```
from machine import Pin, I2C
from time import sleep
from lcd_api import LcdApi
from i2c_lcd import I2cLcd
from keypad import Keypad

# Setup I2C para LCD
i2c = I2C(0, sda=Pin(0), scl=Pin(1), freq=400000)
lcd = I2cLcd(i2c, 0x27, 2, 16)

# Setup del teclado
rows = [Pin(i, Pin.IN, Pin.PULL_DOWN) for i in range(2, 6)]
cols = [Pin(i, Pin.OUT) for i in range(6, 10)]

keys = [
    ["1", "2", "3", "A"],
    ["4", "5", "6", "B"],
    ["7", "8", "9", "C"],
    ["*", "0", "#", "D"]
]

keypad = Keypad(keys, rows, cols)

# Contraseña almacenada
PASSWORD = "1234"
input_code = ""

lcd.clear()
lcd.putstr("Enter password:")

while True:
    key = keypad.get_key()
    if key:
        if key == "#":  # Confirmar
            if input_code == PASSWORD:
                lcd.clear()
                lcd.putstr("Access granted")
                print("Access granted")
            else:
                lcd.clear()
                lcd.putstr("Access denied")
                print("Access denied")
            sleep(2)
            lcd.clear()
            lcd.putstr("Enter password:")
            input_code = ""
        elif key == "*":  # Borrar
            input_code = ""
            lcd.clear()
            lcd.putstr("Enter password:")
        else:
            input_code += key
            lcd.move_to(0, 1)
            lcd.putstr("*" * len(input_code))
    sleep(0.1)
```
### Resultado
https://github.com/user-attachments/assets/a1cd1ebe-9c89-4b97-bd42-645bfb2fd674

## 10.- Alarma con sensor PIR y buzzer:

### Codigo
```
from machine import Pin
from time import sleep, ticks_ms

pir = Pin(15, Pin.IN)
buzzer = Pin(14, Pin.OUT)
led = Pin(13, Pin.OUT)

motion_detected = False
last_detection_time = 0
cooldown = 5000  # 5 segundos en ms

while True:
    current_time = ticks_ms()
    if pir.value() == 1 and not motion_detected:
        motion_detected = True
        last_detection_time = current_time
        buzzer.value(1)
        led.value(1)
        print("¡Movimiento detectado!")
    elif motion_detected and (current_time - last_detection_time) > cooldown:
        motion_detected = False
        buzzer.value(0)
        led.value(0)
        print("Movimiento terminado, esperando nueva detección...")
    sleep(0.1)
```
### Resultado
https://github.com/user-attachments/assets/e4f12fea-d1af-4e62-942a-192a30ccf115
