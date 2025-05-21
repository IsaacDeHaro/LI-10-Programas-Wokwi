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


## 7.- Contador con botón:

### Codigo
```

```
### Resultado


## 8.- Riego automático simple:

### Codigo
```

```
### Resultado


## 9.- Control de acceso con botón:

### Codigo
```

```
### Resultado


## 10.- Mini estación meteorológica:

### Codigo
```

```
### Resultado

