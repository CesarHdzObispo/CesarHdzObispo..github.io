---
layout: home
title: "Recuperar esquema de particiones de un disco duro"
date: 2021-06-30 13:00:00
categories: Ayuda
---

Nota: Es importante que no hayamos realizado la operación que nos mostraba el administrador de discos, debido a que podría ser sobrescrita la información del esquema de particiones que se tenia antes de desconectar la unidad de almacenamiento. 
Nos apoyaremos de la herramienta testdisk la cual entre las características que posee esta integrada una para analizar diferentes tablas de particiones y recuperarlas.


![Screenshot](\assets\images\Picture1.png) <br>
Recuperar esquema de particiones de un disco duro
En la actualidad es muy común enfrentarnos a este tipo de situaciones en las cuales después de desconectar una unidad de almacenamiento (Disco duro externo) de forma incorrecta o cuando se encuentra en uso. Al momento de volver a conectarlo para hacer uso de este y toparnos con la sorpresa de que no es reconocido por nuestro sistema. Y al ingresar en el administrador de discos se nos muestre una ventana como la siguiente.
 
![Screenshot](\assets\images\Picture2.jpg)  <br>
En la cual se nos indica que la unidad lógica necesita ser inicializada. Y es necesario seleccionar un esquema de partición para dicha unidad. Lo primero que se debe hacer en este caso es cancelar dicha operación.
Para luego tratar de recuperar el esquema de particiones que tenia nuestra unidad de almacenamiento antes de desconectarla la última vez.

En primera instancia al momento de ejecutar la herramienta se nos muestra la opción de grabar todo en un archivo existente o nuevo para llevar un control de lo que se esta realizando en la unidad o simplemente no guardar nada. En mi caso como es recuperación de una unidad de almacenamiento propia, no guardare ningún registro. 

![Screenshot](\assets\images\Picture3.jpg)  <br>
Lo siguiente que se necesita hacer es selecciona la unidad de almacenamiento a la que se desea tratar de recuperar el esquema de particiones en mi caso seleccionare la unidad PhysicalDrive2 de tamaño 1000GB.

![Screenshot](\assets\images\Picture4.jpg)  <br>
Una vez seleccionada la unidad, es necesario seleccionar el tipo de esquema de partición que tenia nuestra unidad de almacenamiento. Como dato extra el tipo EFI GPT esta configurado por defecto en la mayoría de los dispositivos de almacenamiento actuales para sistemas Windows. Y el tipo Intel se refiere a un esquema de particiones MBR. En este caso seleccionare el tipo EFI GPT porque es el tipo al que pertenece mi esquema de particiones.

![Screenshot](\assets\images\Picture5.jpg)  <br>
Procedemos a escanear el dispositivo y se nos enlistaran las particiones que se vayan detectando. Una vez terminado este proceso nos movemos a la opción Backup con las flechas del teclado y damos Enter.
En la siguiente lista, podemos seleccionar que particiones nos interesa recuperar, podemos indicar si deseamos que una partición se recupere con las flechas del teclado cambiando la letra P si deseamos la partición y a D si deseamos eliminarla. Después de seleccionar que particiones nos interesa conservar solo basta con dar ENTER.

![Screenshot](\assets\images\Picture6.jpg)  <br>
Por ultimo ya solo faltaría la confirmación de la escritura en el disco para volver a tener las particiones seleccionadas en el proceso.