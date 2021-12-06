---
layout: post
title: "Instalación de ArchLinux [UEFI, Secure Boot, Esquema de particiones personalizado]"
date: 2021-12-06 13:00:00
categories: Linux
---

Este configuracion funciona para tener el sistema en modo uefi con secure boot activado y un esquema de particiones personalizado.

![Mi Sistema](/assets/images/Sistema.png)

### 1. Cargar la distribución del teclado
```bash
loadkeys la-latin1
```

### 2. Configuraciones de Red por WIFI
```bash
# Iniciar el programa iwctl
iwctl
# Listar las interfaces de red.
device list
# Escaneo de redes.
station device scan
station device get-networks
# Conexion con una red.
station device connect SSID
```

### 3. Configuracion de Red por CABLE
```bash
# Revisar los dispositivos de red
ip a
# Agregar dirección IP
ip addr add 192.168.45.100/24 dev [dispositivo]
# Habilitar dispositivo
ip link set [dispositivo] up
# agregar puerto de enlace o Gateway
ip router add default via 192.168.45.1
```

### 4. Zona horaria
```bash
# Ver lista de zona horaria
timedatectl list-timezones
# Establecer la zona horaria
timedatectl set-timezone 
```

### 5. Tabla de particiones
El esquema de particiones **MBR** solo nos permite crear hasta 4 particiones. Es por ese motivo que sera utilizado **GPT** para poder crear mas de 4.
**Nota**: Es importante resaltar que el esquema de particiones mostrado a continuación es basado en mis necesidades. 

Puede cambiar en base a sus necesidades la raiz root, home y tmp. Y crear su particion de intercambio en base a la cantidad de RAM que tienen instalada en su equipo. La unica particion que se recomida dejar igual es /boot/efi.

| Nombre | Cantidad |
| --- | --- |
| /boot/efi | 512MB |
| /swap | 23GB |
| /tmp | 25GB |
| /   | 50GB |
| /home | Restante |


```
# Ingresamos a la configuracion de particiones con fdisk.
fdisk /dev/[sd*] o /dev/[nvme0n*]
# Una vez dentro del menu ingresamos la opcion g para cambiar de MBR a GPT
g
```

```bash
# Comenzamos a crear las particiones correspondientes.
n
+512MB
n
+23GB
n
+25GB
n
+50GB
n
+[Restante]
...
# Con la opcion t cambiamos el tipo de particion.
t
# tipo de partición efi para sda1
# tipo de partición swap para sda2
# tipo de partición home para sda5
# tipo de partición Sistema de ficheros de Linux
```

### 6. Formateo de las particiones

```
mkfs.vfat -F 32 /dev/sda1
mkswap /dev/sda2
mkfs.ext4 /dev/sda3
mkfs.ext4 /dev/sda4
mkfs.ext4 /dev/sda5
```

### 7. Montaje de las particiones /, /tmp, /boot/efi, /home y /swap
La primer partición en ser montada es la **/**. Seguido de eso se crean los directorios para las demas particiones.

```bash
# Montaje de la particion raiz.
mount /dev/sda4 /mnt
# Creacion de los directorios para las otras particiones.
mkdir /mnt/home
mkdir /mnt/boot
mkdir /mnt/boot/efi
mkdir /mnt/tmp
# Montaje de las particiones en sus correspondientes directorios.
mount /dev/sda1 /mnt/boot/efi
mount /dev/sda5 /mnt/home
mount /dev/sda3 /mnt/tmp
# Montaje de la particion SWAP.
swapon /dev/sda2
```

### 7. Configuración de las mirrorlist
Puede consultar los servidores correspondientes a su region geografica en la pagina [mirrorlist](https://archlinux.org/mirrorlist/all/).

```
nano /etc/pacman.d/mirrorlist
# Ejemplo de la mirror list de mexico.
Server = https://arch.jsc.mx/$repo/os/$arch
```

### 8. Instalación de Linux
Para instalar linux solo son necesarios los paquetes **base base-devel linux linux-firmware**. Y para el cargador de arrange los paquetes **grub y efibootmgr**. Para el servicio de Red se necesita el paquete **networkmanager**.  El paquete **wpa_supplicant** es utilizado por el servicio wifi. Por ultimo es necesario tener un editor de texto instalado para poder realizar las ultimas configuraciones en este caso **nano**.
```bash
pacstrap /mnt base base-devel linux linux-firmware xorg grub efibootmgr networkmanager os-prober wpa_supplicant nano
```

### 9. Generación del archivo FSTAB para el montaje automatico de las particiones

```
genfstab -U -p /mnt >> /mnt/etc/fstab
```

### 10. Cambiar al Sistema de archivos instalado.
```bash
arch-chroot /mnt
```

### 11. Configurar el nombre del sistema
```bash
echo "MiSistema" > /etc/hostname
```

### 12. Lenguaje del sistema
[Configuraciones para la distribucion de teclado](https://wiki.archlinux.org/title/Linux_console_(Espa%C3%B1ol)/Keyboard_configuration_(Espa%C3%B1ol)).
```bash
# Establecemos la distribucion de teclado
nano /etc/vconsole.conf
KEYMAP=es

# Configuracion para la distribucion de la consola.
localectl set-keymap --no-convert es

# Tipo de codificación. 
nano /etc/locale.gen
es_MX.UTF-8 UTF-8

# Cambio de la codificación del sistema en base a lo especificado en el archivo /etc/locale.gen
locale-gen
# Creación del archivo locale.conf
echo LANG=es_MX.UTF-8 > /etc/locale.conf
# Creación de la variable LANG
export LANG=es_MX.UTF-8
```

### 13. Zona horaria
```bash
# Configuración de la zona horaria. 
ln -sf /usr/share/zoneinfo/America/Monterrey /etc/localtime

# Obtener la hora de la bios y sincronizarla en el sistema.
hwclock --systohc --utc

# Comprobar hora del sistema.
date
```


### 14. Configuraciones de red

```bash
# Habilitamos los servicios NetworkManager y wpa_supplicant
systemctl enable NetworkManager
systemctl enable wpa_supplicant

# Creación del archivo hosts.
nano /etc/hosts
127.0.0.1	localhost
::1		localhost
127.0.0.1	arch-linux.localdomain	arch-linux
```

### 15. Actualizar sistema
```bash
pacman -Syu
```

### 16. Directorios por defecto.
En archlinux es necesario instalar el paquete **xdg-user-dirs** para que al momento de crear un nuevo usuario en su correspondiente directorio sean creadas sus carpetas por defecto.
```bash
pacman -S xdg-user-dirs
```

### 17. Cuentas de usuario.
```bash
# Cambiamos la contraseña de root
passwd

# Agregamos un nuevo usuario
useradd -mg users -G wheel,storage,power -s /bin/bash cesarhdz

# Establecemos la contraseña para el nuevo usuario.
passwd cesarhdz

# Cambiamos de cuenta de usuario 
su cesarhdz

# Ejecutamos el comanod xdg-user-dirs-update para crear los directorios por defecto.
xdg-user-dirs-update

# Editamos el archivo /etc/sudoers para permitir el uso de sudo desde la cuenta de usuario. Solo es necesario descomentar la linea
%wheel ALL=(ALL) ALL
```

### 18. Creción del gestor de arranque GRUB.
```bash
# Instalamos el grub en el directorio /boot/efi.
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub_uefi --recheck
# Creamos el archivo de configuración.
grub-mkconfig -o /boot/grub/grub.cfg
```

### 19. Interfaz grafica.
Si lo unico que necesita es utilizar la terminal este paso se omite pero si necesita un gestor de ventanas o entorno de escritorio es necesario instalar el que usted prefiera. 

Puede consultar los entornos de escritorio que son compatibles con Archlinux en la siguiente liga [Entornos de escritorio](https://wiki.archlinux.org/title/Desktop_environment_(Espa%C3%B1ol)#X_Window_System).

# Referencias

https://wiki.archlinux.org/title/installation_guide#Pre-installation

https://ugeek.github.io/blog/post/2020-12-26-conectarse-a-una-red-wifi-desde-la-terminal.html

https://www.linuxuprising.com/2019/07/how-to-set-timezone-and-enable-network.html

https://atareao.es/como/particiones-en-linux/ 

http://www.ramip.net/distribuciones-linux/montar-una-particion-ntfs-en-linux.htmlhttp://www.ramip.net/distribuciones-linux/montar-una-particion-ntfs-en-linux.html

https://wiki.archlinux.org/title/XDG_user_directories

https://wiki.archlinux.org/title/Iwd