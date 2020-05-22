# Ubuntu

Este documento tiene como objetivo almacenar comandos de interés para el uso de ubuntu, incluyendo como máquina virtual.

## VirtualBox Guest Additions 🖥

Necesitaremos instalar los paquetes y dependencias necesarios para Virtualbox

```terminal
sudo apt install -y gcc make perl linux-headers-generic virtualbox-guest-dkms
```

Instalar las VBox Guest Additions mediante el CD y reiniciar.

### Carpeta compartida

Si la carpeta compartida no funciona correctamente por problemas de permisos

```terminal
sudo usermod -aG vboxsf <usuario>
```

## Audio entrecortado 🎧

Si el sonido de la máquina virtual sufre cortes es posible reiniciar el servicio de sonido

```terminal
pulseaudio -k && sudo alsa force-reload
```

## Disco montable en barra lateral 💿

Para eliminar el disco montable de google al iniciar la cuenta en ubuntu

```terminal
gsettings set org.gnome.shell.extensions.dash-to-dock show-mounts false
```
