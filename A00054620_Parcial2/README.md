### Examen 2
**Universidad ICESI**  
**Curso:** Sistemas Operativos  
**Docente:** Daniel Barragán C.  
**Tema:** Namespaces, CGroups, LXC  
**Correo:** daniel.barragan at correo.icesi.edu.co  

**Estudiante:** Juan Camilo Swan.  
**Código:** A00054620  
**Correo:** juanswan13 at gmail  

### Objetivos
* Comprender los fundamentos que dan origen a las tecnologías de contenedores virtuales
* Conocer y emplear funcionalidades del sistema operativo para asignar recursos a procesos
* Conocer y emplear capacidades de CentOS7 para la virtualización

### Prerrequisitos
* Virtualbox o WMWare
* Máquina virtual con sistema operativo CentOS7

### Descripción
El segundo parcial del curso sistemas operativos trata sobre el manejo de namespaces, cgroups y virtualización por medio de LXC/LXD

### Actividades
1. Incluir nombre, código (5%)
2. Ortografía y redacción cuando sea necesario (5%)
3. Realice una prueba de concepto empleando systemd y el recurso de control CPUQuota teniendo en cuenta los requerimientos que se describen a cotinuación. Incluya evidencias del funcionamiento de lo solicitado (30%):
 * Las pruebas se realizaran sobre un solo núcleo de la CPU
 * Se deben ejecutar dos procesos
 * Cada proceso debe poder acceder solo al 50% de la CPU
 * Cuando uno de los procesos se cancela, el que continua ejecutándose no debe acceder a mas del 50% de la CPU
4.  Realice una prueba de concepto empleando systemd y el recurso de control CPUShares teniendo en cuenta los requerimientos que se describen a continuación. Incluya evidencias del funcionamiento de lo solicitado (30%):
 * Las pruebas se realizaran sobre un solo núcleo de la CPU
 * Se deben ejecutar dos procesos
 * Uno de los procesos tendrá el 25% de la CPU mientras que el otro tendrá el 75% de la CPU
 * Cuando uno de los procesos se cancela, el que continua ejecutándose debe poder llegar al 100% de la CPU
5. Por medio de las evidencias obtenidas en los puntos anteriores y de fuentes de consulta en Internet, elabore las definiciones para los grupos de control CPUQuota y CPUShares, además concluya acerca de cuando es preferible usar un recurso de control sobre otro (20%)
6. El informe debe ser entregado en formato pdf a través del moodle y el informe en formato README.md debe ser subido a un repositorio de github. El repositorio de github debe ser un fork de https://github.com/ICESI-Training/so-exam2 y para la entrega deberá hacer un Pull Request (PR) respetando la estructura definida. El código fuente y la url de github deben incluirse en el informe (10%)    
  
### solución 
 3. Para empezar, el primer paso fue verificar que la máquina virtual con el sistema operativo CentOS7 solo disponía de 1 núcleo de CPU para esta actividad. Para esto se entra al menú de configuración de Virtualbox:  
        ![][1]  
Una vez que se está seguro de que la máquina virtual solo dispone de un núcleo de CPU, se inicia la máquina y a continuación se debe crear un archivo capaz de estresar la CPU y llevar su consumo al máximo, esto es algo con lo que más adelante se podrá comprobar que se está limitando el uso de CPU a los procesos.  
        ![][2]  
        ![][3]  
Ahora se debe acceder al directorio de systemd ubicado en:
```
/usr/lib/systemd
```
Aquí se podrá configurar la ejecución de los procesos para que estos no sobrepasen el umbral que se les sea establecido, esto se logra primero creando un nuevo servicio y brindándole los permisos necesarios, posteriormente se ingresa al archivo de configuración del servicio y se debe especificar en la sección de  **[Service]** la ruta donde se encuentra el proceso que se quiere ejecutar (que para el caso de esta actividad es “/root/Parcial2/consume_cpu.sh” y el CPUQuote=50%. En ultimas en el archivo de configuración del servicio se debe agregar: 
```
[Service]
ExceStart=/root/Parcial2/consume_cpu.sh
CPUQuote=50%
```

Al ser requerido por la actividad dos procesos, se deben entonces crear dos servicios diferentes por lo que para realizar la actividad se han creado los servicios **consume_cpu.service** y **consume_cpu2.service** . Todo de la siguiente forma:  
        ![][4]  
        ![][5]  
  
Ya toda esta listo para comprobar el funcionamiento de dos procesos reestringidos al 50% de uso de CPU mediante el comando CPUQuote de systemd. Pero primero se debe comprobar el estado de uso de la CPU sin que ningún servicio sea iniciado:  
        ![][6]  
 Al haber realizado cambios en la configuración de los servicios, antes de inicializar los procesos se debe recargar el sistema para lo que se utiliza el comando  
  
```
systemctl daemon-reload
```
  
Y para iniciar los servicios se debe utilizar el comando systemctl seguidor de start y el nombre del servicio que se desea iniciar. Para el caso de este trabajo, se inicializarán los dos procesos al mismo tiempo:  
        ![][7]  
Ahora se comprueba que los dos procesos consume_cpu.sh se encuentran en ejecución con los PID 2702 y 2743 utilizando 49,2% y 49,5% de CPU respectivamente.  
        ![][8]  
Se mata el proceso con PID 2743, dejando únicamente en ejecución al proceso 2743. Se puede observar como aun teniendo disponible el 100% de uso de la CPU, el proceso 2743 no accede a más del 50% de la CPU ya que en su configuración esta que el máximo de CPU que este servicio puede llegar a utilizar es el 50%.  
        ![][9]  
Para demostrar que este proceso consume_cpu.sh si es capaz de utilizar el 100% de la CPU, también se ejecutó sin restricción de CPUQuoute:
        ![][10]  
  
### Referencias
https://github.com/ICESI/so-containers


[1]: images/foto0.PNG
[2]: images/Foto1.PNG
[3]: images/foto2.PNG
[4]: images/foto3.PNG
[5]: images/foto4.PNG
[6]: images/foto5.PNG
[7]: images/foto6.PNG
[8]: images/foto7.PNG
[9]: images/foto8.PNG
[10]: images/foto9.PNG
