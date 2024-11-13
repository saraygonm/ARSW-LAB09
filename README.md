### Escuela Colombiana de Ingeniería
### Arquitecturas de Software - ARSW
#### 👩🏼‍💻👨🏻‍💻 AUTORES: [Saray Alieth Mendivelso Gonzalez](https://github.com/saraygonm) y [Miguel Camilo Tellez Ávila](https://github.com/miguel-tellez)

## Escalamiento en Azure con Maquinas Virtuales, Sacale Sets y Service Plans

### Dependencias
* Cree una cuenta gratuita dentro de Azure. Para hacerlo puede guiarse de esta [documentación](https://azure.microsoft.com/es-es/free/students/). Al hacerlo usted contará con $100 USD para gastar durante 12 meses.

<p align="center">
<img src="images/partRTA/1.png" alt="" width="700px">
</p>

### 📍Parte 0 - Entendiendo el escenario de calidad

Adjunto a este laboratorio usted podrá encontrar una aplicación totalmente desarrollada que tiene como objetivo calcular el enésimo valor de la secuencia de Fibonnaci.

**Escalabilidad**
Cuando un conjunto de usuarios consulta un enésimo número (superior a 1000000) de la secuencia de Fibonacci de forma concurrente y el sistema se encuentra bajo condiciones normales de operación, todas las peticiones deben ser respondidas y el consumo de CPU del sistema no puede superar el 70%.

### 📍Parte 1 - Escalabilidad vertical

1. Diríjase a el [Portal de Azure](https://portal.azure.com/) y a continuación cree una maquina virtual con las características básicas descritas en la imágen 1 y que corresponden a las siguientes:
    * Resource Group = SCALABILITY_LAB
    * Virtual machine name = VERTICAL-SCALABILITY
    * Image = Ubuntu Server 
    * Size = Standard B1ls
    * Username = scalability_lab
    * SSH publi key = Su llave ssh publica

![Imágen 1](images/part1/part1-vm-basic-config.png)

Nota: para llevar a cabo el proceso, antes se debe crear una clave SSH, tal como se muestra a continuación:

<!-- Creación de tabla para alinear las imágenes lado a lado.-->
| <img src="images/partRTA/3.png" alt="Descarga local" width="500px"> | <img src="images/partRTA/2.png" alt="Importar carpetas" width="500px"> |
|---------------------------------------------------------------------|---------------------------------------------------------------|
| **Imagen 1: Descarga local**                                        | **Imagen 2: Importar carpetas**                               |

- Se crea la máquina virtual siguiendo los parametros anteriores 
<p align="center">
<img src="images/partRTA/4.png" alt="" width="700px">
</p>


2. Para conectarse a la VM use el siguiente comando, donde las `x` las debe remplazar por la IP de su propia VM (Revise la sección "Connect" de la virtual machine creada para tener una guía más detallada).

    `ssh scalability_lab@xxx.xxx.xxx.xxx`
- **Primero:** abrimos el simbolo del sistema de nuestro PC.
- **Segundo:** Copiamos la Dirección IP Pública, que aparece en la Opción de `Conectar` 
<p align="center">
<img src="images/partRTA/5.png" alt="" width="700px">
</p>

- **Tercero:** Digitamos en nuestra consola `C:\Users\aliet>ssh -i C:\Users\aliet\Downloads\arquitectura\ARSW-LAB09\Lab9.pem scalability_lab@xxx`
<p align="center">
<img src="images/partRTA/6.png" alt="" width="700px">
</p>
<p align="center">
<img src="images/partRTA/6.1.png" alt="" width="700px">
</p>

3. Instale node, para ello siga la sección *Installing Node.js and npm using NVM* que encontrará en este [enlace](https://linuxize.com/post/how-to-install-node-js-on-ubuntu-18.04/).

- Instalamos nvm, por medio del siguiente comando:
<p align="center">
<img src="images/partRTA/7.png" alt="" width="700px">
</p>

- Reiniciamos el cmd y verificamos la instalacion usando `nvm --version` :

<p align="center">
<img src="images/partRTA/7.1.png" alt="" width="700px">
</p>

- Seguidamente instalamos node `nvm install node`

<p align="center">
<img src="images/partRTA/7.2.png" alt="" width="700px">
</p>

- Por último Verificamos la instalación de TODO `nvm ls`

<p align="center">
<img src="images/partRTA/7.3.png" alt="" width="700px">
</p>

4. Para instalar la aplicación adjunta al Laboratorio, suba la carpeta `FibonacciApp` a un repositorio al cual tenga acceso y ejecute estos comandos dentro de la VM:

    `git clone <your_repo>`

    `cd <your_repo>/FibonacciApp`

    `npm install`

<p align="center">
<img src="images/partRTA/8.png" alt="" width="700px">
</p>

5. Para ejecutar la aplicación puede usar el comando `npm FibinacciApp.js`, sin embargo una vez pierda la conexión ssh la aplicación dejará de funcionar. Para evitar ese compartamiento usaremos *forever*. Ejecute los siguientes comando dentro de la VM.

    ` node FibonacciApp.js`

<p align="center">
<img src="images/partRTA/9.1.png" alt="" width="700px">
</p>


6. Antes de verificar si el endpoint funciona, en Azure vaya a la sección de *Networking* y cree una *Inbound port rule* tal como se muestra en la imágen. Para verificar que la aplicación funciona, use un browser y user el endpoint `http://xxx.xxx.xxx.xxx:3000/fibonacci/6`. La respuesta debe ser `The answer is 8`.

![](images/part1/part1-vm-3000InboudRule.png)

- Creamos un ACL del puerto y lo llamamos `Port_3000`

<p align="center">
<img src="images/partRTA/9.png" alt="" width="700px">
</p>

- En el navegador revisamos que el endpoint funcione:

<p align="center">
<img src="images/partRTA/9.2.png" alt="" width="700px">
</p>


7. La función que calcula en enésimo número de la secuencia de Fibonacci está muy mal construido y consume bastante CPU para obtener la respuesta. Usando la consola del Browser documente los tiempos de respuesta para dicho endpoint usando los siguintes valores:
* 1000000
<p align="center">
<img src="images/partRTA/77.0.png" alt="" width="700px">
</p>

* 1010000
<p align="center">
<img src="images/partRTA/77.2.png" alt="" width="700px">
</p>

* 1020000
<p align="center">
<img src="images/partRTA/77.3.png" alt="" width="700px">
</p>

* 1030000
<p align="center">
<img src="images/partRTA/77.4.png" alt="" width="700px">
</p>

* 1040000
<p align="center">
<img src="images/partRTA/77.5.png" alt="" width="700px">
</p>

* 1050000
<p align="center">
<img src="images/partRTA/77.6.png" alt="" width="700px">
</p>

* 1060000
<p align="center">
<img src="images/partRTA/77.7.png" alt="" width="700px">
</p>

* 1070000
<p align="center">
<img src="images/partRTA/77.8.png" alt="" width="700px">
</p>

* 1080000
<p align="center">
<img src="images/partRTA/77.91.png" alt="" width="700px">
</p>

* 1090000    
<p align="center">
<img src="images/partRTA/77.10.png" alt="" width="700px">
</p>

8. Dírijase ahora a Azure y verifique el consumo de CPU para la VM. (Los resultados pueden tardar 5 minutos en aparecer).

![Imágen 2](images/part1/part1-vm-cpu.png)
- Los resultados:
<p align="center">
<img src="images/partRTA/88.png" alt="" width="700px">
</p>

9. Ahora usaremos Postman para simular una carga concurrente a nuestro sistema. Siga estos pasos.
    * Instale newman con el comando `npm install newman -g`. Para conocer más de Newman consulte el siguiente [enlace](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/).
    * Diríjase hasta la ruta `FibonacciApp/postman` en una maquina diferente a la VM.
    * Para el archivo `[ARSW_LOAD-BALANCING_AZURE].postman_environment.json` cambie el valor del parámetro `VM1` para que coincida con la IP de su VM.
    * Ejecute el siguiente comando.

    ```
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
    newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
    ```

10. La cantidad de CPU consumida es bastante grande y un conjunto considerable de peticiones concurrentes pueden hacer fallar nuestro servicio. Para solucionarlo usaremos una estrategia de Escalamiento Vertical. En Azure diríjase a la sección *size* y a continuación seleccione el tamaño `B2ms`.

![Imágen 3](images/part1/part1-vm-resize.png)

11. Una vez el cambio se vea reflejado, repita el paso 7, 8 y 9.
12. Evalue el escenario de calidad asociado al requerimiento no funcional de escalabilidad y concluya si usando este modelo de escalabilidad logramos cumplirlo.
13. Vuelva a dejar la VM en el tamaño inicial para evitar cobros adicionales.

---------------
❓**Preguntas**

1. ¿Cuántos y cuáles recursos crea Azure junto con la VM?
- Azure crea un total de 7 recursos
  - Clave SSH.
  - Máquina Virtual.
  - Dirección Ip Pública.
  - Grupo de seguridad de red.
  - Red virtual.
  - Interfaz de red
  - Disco
  
<p align="center">
<img src="images/partRTA/pre1/1.png" alt="" width="700px">
</p>

------------------

2. **¿Brevemente describa para qué sirve cada recurso?**

| Recurso                   | Descripción                                                                                              |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| **Clave SSH**             | Credencial que permite la conexión segura y sin contraseña a una máquina virtual.                        |
| **Máquina Virtual**       | Recurso de cómputo que ejecuta sistemas operativos y aplicaciones en un entorno aislado.                 |
| **Dirección IP Pública**   | Dirección única accesible desde internet para que la VM sea alcanzable desde fuera de la red privada.    |
| **Grupo de Seguridad de Red** | Conjunto de reglas de firewall que controla el tráfico de red hacia y desde los recursos asociados.      |
| **Red Virtual**           | Red privada en la nube para conectar varios recursos entre sí.                                           |
| **Interfaz de Red**       | Componente que conecta una VM con la red, permitiendo el envío y recepción de tráfico en la red virtual. |
| **Disco**                 | Almacenamiento persistente que guarda datos de la VM, como sistema operativo, aplicaciones y archivos.   |

--------------
3. **¿Al cerrar la conexión ssh con la VM, por qué se cae la aplicación que ejecutamos con el 
comando `npm FibonacciApp.js`?**
   
-   Al ejecutar una aplicación en la terminal a través de una conexión SSH, esta corre dentro del
    contexto de dicha sesión. Es decir, su funcionamiento depende de que la sesión SSH permanezca activa.
    Por lo tanto, al cerrar la conexión SSH, todos los procesos en ejecución en esa sesión,
    incluyendo la aplicación iniciada (por ejemplo,`npm FibonacciApp.js`), se detendrán.


**¿Por qué debemos crear un *Inbound port rule* antes de acceder al servicio?**

  - Es necesario crear una regla de puerto de entrada para permitir el acceso al servicio desde Internet, ya que Azure bloquea el tráfico entrante por seguridad si no se configura una excepción.


  -  En este caso, fue necesario configurar una regla para permitir el acceso a través del puerto 3000, donde se ejecuta nuestra aplicación. Sin esta regla, Azure bloquearía todo el tráfico hacia ese puerto por motivos de seguridad
-------------
4. Adjunte tabla de tiempos e interprete por qué la función tarda tando tiempo.


------------
5. Adjunte imágen del consumo de CPU de la VM e interprete por qué la función consume esa cantidad de CPU.

--------------
6. Adjunte la imagen del resumen de la ejecución de Postman. Interprete:
    * Tiempos de ejecución de cada petición.
   
    * Si hubo fallos documentelos y explique.

-----------
7. ¿Cuál es la diferencia entre los tamaños `B2ms` y `B1ls` (no solo busque especificaciones de infraestructura)?

-------------------------
**8. ¿Aumentar el tamaño de la VM es una buena solución en este escenario?**
- Aumentar el tamaño de la VM es útil si se cuenta con los recursos económicos,
  ya que una opción como B2ms mejora la CPU, la memoria y reduce tiempos de respuesta comparado
  con B1ls


**¿Qué pasa con la FibonacciApp cuando cambiamos el tamaño de la VM?**
- Según lo mencionado anteriormente, la situación permitiría cálculos más rápidos en FibonacciApp. Sin embargo, esto no resuelve el 
problema de fondo, ya que la aplicación sigue sin estar optimizada.
  - **Una solución ideal** implicaría optimizar el código de FibonacciApp para aprovechar al máximo los recursos, 
  independientemente del tamaño de la VM.
------------

9. **¿Qué pasa con la infraestructura cuando cambia el tamaño de la VM?**
- Cuando cambia el tamaño de la VM, la infraestructura debe adaptarse a los nuevos recursos
asignados, lo que puede implicar un breve tiempo de inactividad mientras se realiza 
la reconfiguración. 

**¿Qué efectos negativos implica?**
- interrupciones en el servicio
- Aumento en los costos operativos
- Mayor consumo de recursos que podría no estar completamente
  justificado si la aplicación no está optimizada, lo que reduciría la eficiencia general

-------------
10. **¿Hubo mejora en el consumo de CPU o en los tiempos de respuesta? Si/No ¿Por qué?**
- Sí, hubo una mejora en el consumo de CPU y en los tiempos de respuesta, debido a que al aumentar los recursos disponibles, la VM puede procesar las
peticiones de forma más eficiente y rápida, reduciendo la carga en cada solicitud.

------------
11. Aumente la cantidad de ejecuciones paralelas del comando de postman a `4`. ¿El comportamiento del sistema es porcentualmente mejor?

### 📍 Parte 2 - Escalabilidad horizontal

#### Crear el Balanceador de Carga

Antes de continuar puede eliminar el grupo de recursos anterior para evitar gastos adicionales y realizar la actividad en un grupo de recursos totalmente limpio.

1. El Balanceador de Carga es un recurso fundamental para habilitar la escalabilidad horizontal de nuestro sistema, por eso en este paso cree un balanceador de carga dentro de Azure tal cual como se muestra en la imágen adjunta.

![](images/part2/part2-lb-create.png)

2. A continuación cree un *Backend Pool*, guiese con la siguiente imágen.

![](images/part2/part2-lb-bp-create.png)

3. A continuación cree un *Health Probe*, guiese con la siguiente imágen.

![](images/part2/part2-lb-hp-create.png)

4. A continuación cree un *Load Balancing Rule*, guiese con la siguiente imágen.

![](images/part2/part2-lb-lbr-create.png)

5. Cree una *Virtual Network* dentro del grupo de recursos, guiese con la siguiente imágen.

![](images/part2/part2-vn-create.png)

#### Crear las maquinas virtuales (Nodos)

Ahora vamos a crear 3 VMs (VM1, VM2 y VM3) con direcciones IP públicas standar en 3 diferentes zonas de disponibilidad. Después las agregaremos al balanceador de carga.

1. En la configuración básica de la VM guíese por la siguiente imágen. Es importante que se fije en la "Avaiability Zone", donde la VM1 será 1, la VM2 será 2 y la VM3 será 3.

![](images/part2/part2-vm-create1.png)

2. En la configuración de networking, verifique que se ha seleccionado la *Virtual Network*  y la *Subnet* creadas anteriormente. Adicionalmente asigne una IP pública y no olvide habilitar la redundancia de zona.

![](images/part2/part2-vm-create2.png)

3. Para el Network Security Group seleccione "avanzado" y realice la siguiente configuración. No olvide crear un *Inbound Rule*, en el cual habilite el tráfico por el puerto 3000. Cuando cree la VM2 y la VM3, no necesita volver a crear el *Network Security Group*, sino que puede seleccionar el anteriormente creado.

![](images/part2/part2-vm-create3.png)

4. Ahora asignaremos esta VM a nuestro balanceador de carga, para ello siga la configuración de la siguiente imágen.

![](images/part2/part2-vm-create4.png)

5. Finalmente debemos instalar la aplicación de Fibonacci en la VM. para ello puede ejecutar el conjunto de los siguientes comandos, cambiando el nombre de la VM por el correcto

```
git clone https://github.com/daprieto1/ARSW_LOAD-BALANCING_AZURE.git

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash
source /home/vm1/.bashrc
nvm install node

cd ARSW_LOAD-BALANCING_AZURE/FibonacciApp
npm install

npm install forever -g
forever start FibonacciApp.js
```

Realice este proceso para las 3 VMs, por ahora lo haremos a mano una por una, sin embargo es importante que usted sepa que existen herramientas para aumatizar este proceso, entre ellas encontramos Azure Resource Manager, OsDisk Images, Terraform con Vagrant y Paker, Puppet, Ansible entre otras.

#### Probar el resultado final de nuestra infraestructura

1. Porsupuesto el endpoint de acceso a nuestro sistema será la IP pública del balanceador de carga, primero verifiquemos que los servicios básicos están funcionando, consuma los siguientes recursos:

```
http://52.155.223.248/
http://52.155.223.248/fibonacci/1
```

2. Realice las pruebas de carga con `newman` que se realizaron en la parte 1 y haga un informe comparativo donde contraste: tiempos de respuesta, cantidad de peticiones respondidas con éxito, costos de las 2 infraestrucruras, es decir, la que desarrollamos con balanceo de carga horizontal y la que se hizo con una maquina virtual escalada.

3. Agregue una 4 maquina virtual y realice las pruebas de newman, pero esta vez no lance 2 peticiones en paralelo, sino que incrementelo a 4. Haga un informe donde presente el comportamiento de la CPU de las 4 VM y explique porque la tasa de éxito de las peticiones aumento con este estilo de escalabilidad.

```
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10 &
newman run ARSW_LOAD-BALANCING_AZURE.postman_collection.json -e [ARSW_LOAD-BALANCING_AZURE].postman_environment.json -n 10
```

❓**Preguntas**

* ¿Cuáles son los tipos de balanceadores de carga en Azure y en qué se diferencian?, ¿Qué es SKU, qué tipos hay y en qué se diferencian?, ¿Por qué el balanceador de carga necesita una IP pública?
* ¿Cuál es el propósito del *Backend Pool*?
* ¿Cuál es el propósito del *Health Probe*?
* ¿Cuál es el propósito de la *Load Balancing Rule*? ¿Qué tipos de sesión persistente existen, por qué esto es importante y cómo puede afectar la escalabilidad del sistema?.
* ¿Qué es una *Virtual Network*? ¿Qué es una *Subnet*? ¿Para qué sirven los *address space* y *address range*?
* ¿Qué son las *Availability Zone* y por qué seleccionamos 3 diferentes zonas?. ¿Qué significa que una IP sea *zone-redundant*?
* ¿Cuál es el propósito del *Network Security Group*?
* Informe de newman 1 (Punto 2)
* Presente el Diagrama de Despliegue de la solución.




