# RNSES-Cynthia-P3

## Tabla de contenidos 
* [Información](#info)
* [Tarea 1. Conectar Smart IMU](#tarea1)
* [Tarea 2. Mostrar datos en tiempo real ](#tarea2)
* [Tarea 3. Almacenar datos en fichero .txt](#tarea3)
* [Tarea 4. Calculos y menejo de datos](#tarea4)

## Información
Punto de partida con Python: https://uniwebsidad.com/libros/python

* Documentación Python: https://docs.python.org/3/
* Sistema operativo: macOS Catalina versión 10.15.7
* Version Python: Python 3.9.12 (Entorno Anaconda 3 - Spyder)
* CSV: https://docs.python.org/es/3/library/csv.html

## Librerias
![conda install numpy](https://github.com/Cynthia-696529/Imagenes/blob/main/Captura%20de%20pantalla%202022-08-19%20a%20las%2019.18.01.png)

![conda install matplotlib](https://github.com/Cynthia-696529/Imagenes/blob/main/Captura%20de%20pantalla%202022-08-19%20a%20las%2019.19.15.png)

![conda install pyserial](https://github.com/Cynthia-696529/Imagenes/blob/main/Captura%20de%20pantalla%202022-08-19%20a%20las%2019.23.21.png)

## Tareas 1-2
* Tarea 1
Conectar el Smart IMU y comprobar la recepción de datos en el puerto serie
* Tarea 2 
Acceder al puerto serie y mostrar por pantalla los datos en tiempo real.
* Código
 ```
 #!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Tarea 1 Conectar Smart IMU y comprobar la recpeción de datos del puerto serie
"""

import serial
import time

port = "/dev/cu.SLAB_USBtoUART"
baudio = 115200 #baurate
esp32 = serial.Serial(port,baudio)
time.sleep(2)                         # espero 2s para que se acople el puerto serie

### VARIABLES ###
cont = 0

while True:    
    while cont>10:                      # cuando llega a este valor, la señal recibida por el sensor es estable 
        salida = esp32.readline()       # devuelve un número binario
        salida = salida.decode("utf-8") # cast de binario a string
        print(salida)
    cont = cont +1;
 ```
 
* Video
[![Conec.IMU](https://img.youtu.be/VO3m8w6JL4U.jpg)](https://youtu.be/VO3m8w6JL4U)

## Tarea 3
Almacenar los datos en un fichero .txt separando cada variable con ";", y con retorno de carro al final de cada muestra.
* Código
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Tarea 3: Almacenar datos en un fichero .txt separando las variable por ';'
"""

import serial
import time
import csv # archivos csv
port = "/dev/cu.SLAB_USBtoUART"
baudio = 115200 #baurate
esp32 = serial.Serial(port,baudio)
time.sleep(2)                         # espero 2s para que se acople el puerto serie

### VARIABLES ###
cont = 0
data=[]
name_data ="datos.txt"


with open(name_data,'w',newline='') as csvFile:

    writer = csv.writer(csvFile,delimiter=';')
    while True:   
        salida = esp32.readline()          # devuelve un número binario
        salida = salida.decode("utf-8")    # cast de binario a string
        salida = salida.replace(",", ";"); # reemplazar ',' por ';'       
        data.append(salida)               # añade los elementos salida a la lista datos[]
    
        if(cont>10):                       # cuando llega a este valor, la señal recibida por el sensor es estable 
            a,b,c= data[cont].split(";")  # divide la cadena cada vez aue ve ';'    
            writer.writerow([float(a),float(b),float(c)])                             
        cont=cont+1
        if(cont>110):
            break     
 
esp32.close()
csvFile.close()
```
## Tarea 4
Acumular datos durante 5 seg, calcular el promedio y desviación estándar y representarlos gráficamente.
