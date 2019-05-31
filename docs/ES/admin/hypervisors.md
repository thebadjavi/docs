<h1>Hypervisors</h1>

[TOC]

# Tipos de hipervisores

## Hipervisor puro

Cuando crea o edita un hipervisor, puede verificar si será solo un hipervisor. En este modo, el servidor solo será utilizado por IsardVDI para ejecutar escritorios virtuales. Este servidor no se encargará de ninguna operación de discos virtuales.

## Operaciones de disco puro

En este modo, el servidor solo se utilizará para gestionar las operaciones de disco de los escritorios virtuales.

## Hipervisor mixto + operaciones de disco

Este modo utilizará el servidor tanto para ejecutar escritorios virtuales como para ejecutar operaciones de disco virtual.

# Hipervisores externos

El método preferido para configurar un nuevo hipervisor es usar la imagen docker isard-generic-hyper ya que mostrará todos los servicios necesarios en cualquier host linux con docker. Se requieren tres pasos:

1. Instalar hipervisor KVM (se recomienda docker)
2. Añadir claves ssh para el nuevo hipervisor KVM
3. Cree un nuevo hipervisor en la interfaz web de IsardVDI.

## Instalar hipervisor KVM

### Docker Install (preferred)

La configuración rápida y sencilla de un hipervisor externo se puede realizar utilizando la imagen external_hyper.   En su servidor Linux instalado y limpio que será el contenedor de inicio del hipervisor KVM:

```
cd dockers/external_hyper
docker-compose up -d
```

Esto debería mostrar un hipervisor Isard completo en su servidor. 

Actual certificates in IsardVDI path ```/opt/isard/certs/default/``` must be copied to your new KVM server host created path ```/opt/isard/certs/default``` and container restarted with ```docker-compose restart```.

Ejecute este comando para restablecer las claves ssh y la contraseña:

```bash
docker exec -e PASSWORD=<YOUR_ROOT_PASSWORD> externalhyper_isard-hypervisor_1 bash -c '/reset-hyper.sh'
```

Después puedes continuar añadiendo claves ssh para este hyper del contenedor IsardVDI isard-app y crear un nuevo hipervisor en la interfaz de usuario:

```bash
docker exec -e HYPERVISOR=<IP|DNS> -e PASSWORD=<YOUR_ROOT_PASSWD> -e PORT=2022 isard_isard-app_1 bash -c '/add-hypervisor.sh'; history -d $((HISTCMD-1))
```

### Instalación manual

Si desea utilizar otro host como el hipervisor KVM, asegúrese de que tiene:

- **libvirtd**:
  - * **service running**: ```systemctl status libvirtd```. Verifique con su distro cómo instalar un hipervisor KVM completo:

      * https://wiki.debian.org/KVM#Installation
      * https://help.ubuntu.com/community/KVM/Installation
      * https://wiki.centos.org/HowTos/KVM
      * https://wiki.alpinelinux.org/wiki/KVM

    * **certificados habilitados**: Certificados IsardVDI actuales en la ruta  ```/opt/isard/certs/default/```debe copiar en la nueva ruta de servidor KVM  ```/etc/pki/libvirt-spice```. También deben añadirse algunas líneas a *libvirtd.conf* y *qemu.conf* para hacer uso de los certificados:

      * ```bash
        echo "listen_tls = 0" >> /etc/libvirt/libvirtd.conf; 
        echo 'listen_tcp = 1' >> /etc/libvirt/libvirtd.conf;
        echo 'spice_listen = "0.0.0.0"' >> /etc/libvirt/qemu.conf
        echo 'spice_tls = 1' >> /etc/libvirt/qemu.conf
        ```

    * **servidor websocket**:Para permitir la conexión a los visores a través del navegador, también debe configurar un servidor websocket.  Debes mirar el archivo *start_proxy.py* incluido en dockers/hipervisor
- **Servicio sshd en ejecución y accesible para el usuario**: ```systemctl status sshd```
- **curl**: Se utilizará para obtener actualizaciones.
- **qemu-kvm**: Deberías crear un enlace a tu qemu-system-x86_64 similar a este ```ln -s /usr/bin/qemu-system-x86_64 /usr/bin/qemu-kvm```

## Añadir claves ssh para el nuevo hipervisor 

Debe agregar previamente claves ssh para acceder a ese hipervisor. Nosotros ofrecemos  un script dentro del contenedor isard-app para hacer esto:

```bash
docker exec -e HYPERVISOR=<IP|DNS> -e PASSWORD=<YOUR_ROOT_PASSWD> isard_isard-app_1 bash -c '/add-hypervisor.sh'
```

**Nota**: Hay parámetros opcionales que se pueden configurar:

- PORT:  El valor predeterminado es *22*
- USER:  El valor predeterminado es *root*

Después de hacer esto, debe ir al menú de hipervisores y añadir un nuevo hipervisor con la misma IP/DNS. El motor intentará conectarse cuando habilite ese hipervisor.

**Ejemplo:**

Tenga en cuenta que al agregar ``; history -d $ ((HISTCMD-1)) `` al comando para evitar que la contraseña se guarde en nuestro historial.

```bash
root@server:~/isard# docker exec -e HYPERVISOR=192.168.0.114 -e PASSWORD=supersecret isard_isard-app_1 bash -c '/add-hypervisor.sh'; history -d $((HISTCMD-1))
fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.8/community/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/edge/testing/x86_64/APKINDEX.tar.gz
(1/1) Installing sshpass (1.06-r0)
Executing busybox-1.28.4-r2.trigger
OK: 235 MiB in 127 packages
Trying to ssh into 192.168.0.114...
# 192.168.0.114:22 SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.1
# 192.168.0.114:22 SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.1
# 192.168.0.114:22 SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.1
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
expr: warning: '^ERROR: ': using '^' as the first character
of a basic regular expression is not portable; it is ignored

/usr/bin/ssh-copy-id: WARNING: All keys were skipped because they already exist on the remote system.
		(if you think this is a mistake, you may want to use -f option)

Hypervisor ssh access granted.
Access to 192.168.0.114 granted and found libvirtd service running.
Now you can create this hypervisor in IsardVDI web interface.
```

Si falla, deshabilite el hipervisor, edite los parámetros y vuelve a habilitarlo. El motor IsardVDI comprobará la conexión de nuevo. Puede ver el mensaje de error en los detalles del hipervisor (haga clic en el botón + para ver los detalles).


Si el motor no está intentando conectarse nuevamente después de habilitar el nuevo hipervisor, reinicia el contenedor de la aplicación isard:

```bash
docker restart isard_isard-app_1
```

**NOTA**: Es probable que desee eliminar el hipervisor de isard-hypervisor predeterminado después de crear un nuevo hipervisor externo. Desactiva el  isard-hypervisor y eliminalo de los de detalles. se puede *forzar la eliminacion* y reiniciar el contenedor isard-app después.

## Agregar hipervisor en la interfaz web



# Conceptos de infraestructura

Cuando añada un hipervisor externo, debe tener en cuenta (y configurar según sea necesario):

- **ruta de los discos**: Por defecto almacenará los discos en /opt/isard. No es necesario crear esta carpeta, pero se recomienda que el almacenamiento rápido de E/S (como un disco nvme) esté montado en esa ruta.
- **Rendimiento de la red**: Si va a utilizar una  NAS tenga en cuenta que debe utilizar una red rápida. Velocidad de red recomendada superior a 10Gbps.
## Rendimiento de los discos

Como se utilizan diferentes rutas para bases, plantillas y discos de grupos, podría mejorar el rendimiento montando diferentes discos nvme de almacenamiento en cada ruta:

- **/opt/isard/bases**:Aquí  se almacenaran las imágenes base que crees. Por lo general, esas imágenes de disco se utilizarán como base para muchas plantillas y discos de usuario (grupos), por lo que el almacenamiento debe proporcionar un acceso de lectura lo más rápido posible.
- **/opt/isard/templates**: Las plantillas también se utilizarán como discos principales para múltiples discos de usuario (grupos). Así que mejor usa un disco de lectura rápida.
- **/opt/isard/groups**: Running desktops will reside here. So a lot of concurrent IOs will be done here so the storage should provide as much IOPS as possible. NVME disks (or even NVME raids) will bring the best performance.

## Rendimiento de la red

La red de almacenamiento entre hipervisores y servidores de almacenamiento NAS debe ser de por lo menos 10 Gbps. Todos los hipervisores deben montar el almacenamiento en la ruta **/isard** y este debe ser el mismo para todos los hipervisores.

Como puedes elegir si deseas que el hipervisor sea un **hipervisor puro**, **operaciones de disco puro** o uno **mezclado**, puedes añadir servidores de almacenamiento NAS para operaciones de disco puro. Esto traerá un trabajo más rápido de los discos ya que IsardVDI hará uso de almacenamiento directo y con comandos NAS.

Disponemos de una infraestructura IsardVDI con seis hipervisores y dos pacemaker agrupados con NAS de fabricación propia que comparten almacenamiento con NFSv4 con buen rendimiento.

## High Availability / High Performance tech docs

Tratamos de mantener todos nuestros conocimientos y experiencia en el alto rendimiento y disponibilidad de los clústeres que usan pacemaker, drbd, disco cachés, las migraciones en vivo, etc. en la [thedocs] (http://thedocs.isardvdi.com) web de tecnología de IsardVDI.

No te pierdas nuestros videos sobre [escritorio virtual en vivo y almacenamiento](http://thedocs.isardvdi.com/clusters/live-migration/) migración que hicimos en nuestra infraestructura: migrando el escritorio virtual de un hipervisor a otro mientras que AL MISMO TIEMPO migrábamos su almacenamiento de un almacenamiento NAS a otro sin que el usuario del escritorio virtual se diera cuenta de lo que había sucedido!.