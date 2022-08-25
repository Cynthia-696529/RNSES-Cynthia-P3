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
* https://matplotlib.org/stable/api/_as_gen/matplotlib.axes.Axes.plot.html#matplotlib.axes.Axes.plot
* https://matplotlib.org/stable/plot_types/basic/plot.html#sphx-glr-plot-types-basic-plot-py
 
* Código
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Tarea 5 : Acumular datos durante 5 seg, calcular el promedio y desviación estándar y representarlos gráficamente.
"""
import csv
import serial
import time
import matplotlib.pyplot as plot #graficar los datos
import statistics # calculo promedio y desv, estandar

port = "/dev/cu.SLAB_USBtoUART"
baudio = 115200 #baurate
esp32 = serial.Serial(port,baudio)
time.sleep(2)     

### VARIABLES ###
cont = 0
data = []
name_data ="datos.txt"
#ejes
accx=[]   
accy=[]
accz=[]
# promedio
meanx=[]
meany=[]
meanz=[]
# desv.estandar
devx=[]
devy=[]
devz=[]
t=[]
# Plot
plot.figure()
plot1 = plot.subplot(2,2,1)
plot2 = plot.subplot(2,2,2)
plot3 = plot.subplot(2,2,3)

#------------------------------------------------
with open(name_data,'w',newline = '') as csvFile:

    writer = csv.writer(csvFile,delimiter=';')
    while True:   

        in_put = esp32.readline()
        in_put = in_put.decode("utf-8")  # ser.readline returns a binary, convert to string
        in_put = in_put.replace(",", ";");
       
        data.append(in_put)
    
        if(cont>10):
            a,b,c= data[cont].split(";") 
            t.append(cont-10)
            accx.append(float(a))   
            accy.append(float(b))
            accz.append(float(c)) 
            writer.writerow([float(a),float(b),float(c)]) 
            
            meanx.append(statistics.mean(accx))
            meany.append(statistics.mean(accy))
            meanz.append(statistics.mean(accz))
            
            devx.append(statistics.pstdev(accx))
            devy.append(statistics.pstdev(accy))
            devz.append(statistics.pstdev(accz))
            
        cont = cont+1
        if(cont > 110):
            break
#---Plots  -----------------------------------   
    
plot1.plot(t,accx,color='red', label='Eje x')
plot1.plot(t,accy,color='blue', label='Eje y')
plot1.plot(t,accz,color='lightseagreen', label='Eje z')

plot2.plot(t,meanx,color='red', label='Eje x')
plot2.plot(t,meany,color='blue', label='Eje y')
plot2.plot(t,meanz,color='lightseagreen', label='Eje z')

plot3.plot(t,devx,color='red', label='Eje x')
plot3.plot(t,devy,color='blue', label='Eje y')
plot3.plot(t,devz,color='lightseagreen', label='Eje z')

plot1.set_title(" Medidas ")
plot1.set_ylabel(" a (m/s2)")
plot1.set_xlabel("sample number")    
#plot1.legend(bbox_to_anchor=(1.05, 0.5), loc='upper left', borderaxespad=0.)   

plot2.set_title(" Media ")
plot2.set_ylabel(" a (m/s2)")
plot2.set_xlabel("sample number")    
#plot2.legend(bbox_to_anchor=(1.01, 1), loc='upper left', borderaxespad=0.)    

plot3.set_title(" Desv. tipica ")
plot3.set_ylabel(" a (m/s2)")
plot3.set_xlabel("sample number")    
plot3.legend(bbox_to_anchor=(1.01, 1), loc='upper left', borderaxespad=0.)     

print("Fin")
esp32.close()
csvFile.close()


```
* Grafica
![Calculos](https://github.com/Cynthia-696529/Imagenes/blob/d0db84c33e729619e7062e3f84c76fb066d9401c/calculos.png)
