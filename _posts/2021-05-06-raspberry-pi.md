
---
current: post
cover: assets/images/raspberry.jpg
navigation: True
title: Raspberry Pi (Lite + Xfce)
date: 2021-01-29 10:18:00
tags: Tutorial
class: post-template
subclass: 'post'
author: aalmeidar
---

# Introducción

En esta entrada, se explicará la instalación del sistema operativo Raspberry Pi Lite, como implementar Xfce, como medir la temperatura de nuestro dispositivo y automzatizarlo, etc. 
En primer lugar, puede surgir la pregunta ¿Qué es una Raspberry Pi?. De una manera sintetizada, el dispositivo, concretamente, Raspberry Pi 4 8gb es una computadora de una sola placa de bajo coste con múltiples funciones. En este caso, se centrará su atención a su uso como PC. 
Cuenta con las siguiente especificaciones:
- 8GB LPDDR4-3200 SDRAM
- 2.4 GHz and 5.0 GHz IEEE 802.11ac wireless, Bluetooth 5.0, BLE
- USB 3.0 ports; 2 USB 2.0 ports
- Gigabit Ethernet
- Raspberry Pi standard 40 pin GPIO header
- 2 × micro-HDMI ports 
- Broadcom BCM2711, Quad core Cortex-A72 (ARM v8) 64-bit SoC @ 1.5GHz

# Sistema Operativo

Para comenzar, se debe instalar el sistema operativo en una micro sd. Para ello, está la opción de software como [Balena Etcher](https://www.balena.io/etcher/) o de la web oficial de [Raspberry Pi](https://www.raspberrypi.org/software/). Seguidamente, habra que descargarse el sistema operativo, en este caso, optaremos por [Raspberry Pi Os Lite](https://downloads.raspberrypi.org/raspios_lite_armhf/images/raspios_lite_armhf-2021-05-28/2021-05-07-raspios-buster-armhf-lite.zip) que se puede encontrar en la web (Si se ha escogido el software oficial este ya trae de por sí facilidades para descargar algunos de los sistemas disponibles).

Una vez hecho esto ya estará listo el sistema para usarlo. Atención, el usuario y la contraseña son por defecto pi:raspberry.

# Software

Antes que nada, debe hacerse una actualización al sistema. Al estar en Rasperry Pi OS Lite, se puede usar el gestor de paquetes apt.

```bash
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get dist-upgrde
$ sudo reboot
```
Si se observa cualquier error relacionado con la conexión revisela con el comando raspi-config, desde ahí podrá manejar de una forma fácil la configuración del dispositivo,

Posteriormente de configurar de manera correcta todo, se instala xorg y xinit (display server).
```bash
$ sudo apt-get install --no-install-recommends xserver-xorg
$ sudo apt-get install --no-install-recommends xinit
```
 Y por último, para terminar con la parte de la interfaz gráfica, se instala nuestro preferido. Aqui continuaremos con Xfce.
```bash 
$ sudo apt-get install xfce4 xfce4-terminal
```
Una vez se reinicie, el inicio de sesión aparecerá en pantalla.

# Extra

- ¿Como se mide la temperatura de la Raspberry Pi? 
Para medir la temperatura se toman dos valores el de la cpu y gpu. Para poder ver la temnperatura de la cpu, se usa:
```bash
$ cat /sys/class/thermal/thermal_zone0/temp
```
Y para la gpu:
```bash
$ vcgencmd measure_temp 
```
Pero este proceso se puede amenizar con el siguiente script en bash:
```bash
#!/usr/bin/bash

cpu=$(</sys/class/thermal/thermal_zone0/temp)
let cpu=$((cpu/1000))  
gpu=$(vcgencmd measure_temp | cut -d= -f2 | cut -d. -f1  )

endColour="\033[0m\e[0m"
blueColour="\e[0;34m\033[1m"

if [[ $cpu -lt 60 ]]; then
	cpuColour="\e[0;32m\033[1m"
elif [[ $cpu -lt 85 ]]; then
	cpuColour="\e[0;33m\033[1m"
else 
	cpuColour="\e[0;31m\033[1m"
fi

if [[ $gpu -lt 60 ]]; then   
        gpuColour="\e[0;32m\033[1m"             
elif [[ $gpu -lt 85 ]]; then   
        gpuColour="\e[0;33m\033[1m"
else    
	gpuColour="\e[0;31m\033[1m"
fi  


echo -e "\n${blueColour}Temperature Raspberry Pi 4${endColour}"
echo -e "${blueColour}-----------------------------------${endColour}"
echo -e "\n\t${blueColour}CPU => ${cpuColour} $cpu ºC${endColour}"
echo -e "\t${blueColour}GPU => ${gpuColour} $gpu ºC${endColour}\n"
```

Espero que haya servido de ayuda.
