<h1>Instalación</h1>

IsardVDI ahora funciona fuera del docker & del docker-compose

[TOC]

# Requisitos

Solo necesitas tener instalado el
servicio docker y el docker-compose:

- Instala el **Docker**: https://docs.docker.com/engine/installation/

  - Nota:es necesario la versión 17.04 del docker o mayor para el docker-compose.yml v3.2

    - ```bash
      sudo apt install docker
      ```

- Instala el **docker-compose**: https://docs.docker.com/compose/install/

  - Nota: es necesario la versión 1.12 del docker-compose o  mayor para el docker-compose.yml v3.5. Puedes instalar la última versión usando pip3:

    - ```bash
      sudo apt install python3-pip -y
      sudo pip3 install docker-compose
      ```

**Nota**: Su hardware necesita tener la virtualización habilitada. Puedes comprobarlo en la BIOS o en CLI:

```
egrep ‘(vmx|svm)’ /proc/cpuinfo
```
​Si no ve nada en la salida, su CPU no tiene capacidades de virtualización o están deshabilitadas en el BIOS.

# Inicio rápido

Para que aparezca IsardVDI, solo necesitas descargar el archivo docker-compose.yml (o clonar el repositorio completo si deseas compilar las imágenes) y abrirlo:

```bash
wget https://raw.githubusercontent.com/isard-vdi/isard/master/docker-compose.yml
docker-compose pull
docker-compose up -d
```

Eso es todo, solo conéctate a **https://<ip|domain>** servidor y sigue el [asistente] (wizard.md).

Nota: Si se conecta con el navegador al asistente a través del *localhost* o *127.0.0.1* el  **nombre del host** el *isard-hypervisor* se ajustará a esa IP y nadie será capaz de abrir los visores de otros equipos. Consulta  [el solucionador de problemas del hostname incorrecto](../admin/faq.md#tries-to-connect-to-localhost-or-incorrect-iphostname) to modify the viewer IP.

# Insights

## Rutas mapeadas


IsardVDI creará las siguientes rutas en su sistema y las asignará dentro de los hipervisores y los contenedores de las aplicaciones:


- **/opt/isard**: La carpeta principal que contendrá:
  - **bases**:Ruta donde se almacenarán las imágenes de la plantilla base. La ruta completa incluye `/opt/isard/bases/<role>/<category>/<group>/<username>/<base_disk_name.qcow2>`
  - **templates**: Ruta donde se almacenarán las imágenes de la plantilla del usuario. La ruta completa incluye `/opt/isard/templates/<role>/<category>/<group>/<username>/<tmpl_disk_name.qcow2>`
  - **groups**: Ruta donde se almacenarán las imágenes de escritorio ejecutables. La ruta completa incluye `/opt/isard/group/<role>/<category>/<group>/<username>/<desktop_disk_name.qcow2>`
  - **media**:Ruta donde se cargarán los medios (archivos iso y floppy). La ruta completa incluye `/opt/isard/media/<role>/<category>/<group>/<username>/<media_(iso|floppy).(iso|fd)>`
  - **backups**: Las copias de seguridad de la base de datos creadas en la interfaz web utilizando el menú de configuración de copia de seguridad se almacenarán aquí.
  - **uploads**: (trabajo en progreso)
  - **logs**: Aquí tendrás registros para todos los contenedores. por lo que se deberían rotar / eliminar de programatically.
  - **certs**:Aquí se almacenan los certificados para la interfaz de usuario web y las conexiones del visor. También puedes reemplazar los certificados autofirmados con sus comerciales / permite cifrarlos siguiendo la guía de documentación sobre [reemplazo de certificados] (certificates.md). En la versión actual, en la web de IsardVDI y los usuarios hacen uso de los mismos certificados almacenados en la ubicación de la ruta `/ opt / isard / certs / default /`.

## Construye tus imágenes docker

Si prefiere crear sus imágenes de docker basadas en alpine. Las imágenes que tiene que clonar están en el repositorio (git clone https://github.com/isard-vdi/isard.git) encontrará las fuentes de la ventana docker en la carpeta de la ventana docker:

- **alpine-pandas**: Esta imagen tiene las bibliotecas de python3 de pandas y numpy. Las compilaciones de esas bibliotecas llevan mucho tiempo, como son necesarias para los hipervisores y los contenedores de aplicaciones, decidimos crear esta imagen como base para esas imágenes.
- **hypervisor**: Tomará la imagen de alpine-pandas  como base y abrirá un hipervisor  KVM/qemu  completo con libvirt y un proxy websockets de Python para el acceso del navegador a los visores.
- **app**:Agregará todas las bibliotecas necesarias para ejecutar el motor y el código fuente IsardVDI de la aplicación web el codigo esta en la carpeta /src  de repo.
- **nginx**: Tiene el servidor web https optimizado para socketio y websockets. También tiene fuentes para páginas de error y websocket vnc y visores de especias.
- **rethinkdb**: 
41/5000
Usamos la imagen oficial de rethinkdb de https://hub.docker.com/_/rethinkdb/.

Proporcionamos un script de compilación para los dockers. Solo necesitas agregar el parámetro de la versión:

```bash
./build-docker-images.sh 1.0.1
```
Después de construir imágenes desde la fuente puedes comenzar con ```docker-compose up -d```.

Nota: Verifica la versión de los contenedores en el archivo docker-compose.yml para compilar la misma versión.
# Ejemplo de instalación

## Debian 9 Stretch

Con una nueva instalación de Debian 9 puedes instalar docker y docker-compose con estos comandos.

### Instalar docker

```bash
apt-get remove docker docker-engine docker.io containerd runc
apt-get install     apt-transport-https     ca-certificates     curl     gnupg2     software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
apt-get update
apt-get install docker-ce
```

### Instalar docker-compose
```bash
apt install python3-pip
pip3 install docker-compose
```
## Fedora 28-29

Con la nueva instalación de Fedora 28-29, puede instalar el docker and docker-compose con estos comandos.

### Instalar docker

```bash
sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine

sudo dnf -y install dnf-plugins-core
sudo dnf config-manager \
    --add-repo \
    https://download.docker.com/linux/fedora/docker-ce.repo
sudo dnf install docker-ce docker-ce-cli containerd.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

### Instalar docker-compose
```bash
yum install python3-pip
pip3 install docker-compose
```

# Solucionar problemas de instalación

## docker-compose up -d se niega a iniciar el hipervisor

Puede haber dos posibles causas para este problema. Uno es que  el uso de un servicio del host se encuentra en un puerto en el rango de puertos predeterminados utilizados por el hipervisor y los visores. Esos puertos son:

- 5900-5949: Spice viewer port range
- 55900-55949: web browser viewer ports.

Puedes verificar los puertos de escucha con el comando **netstat -tulpn** y comprobar si alguno de sus puertos de escucha se  superpone con el intervalo de puertos hipervisor.

No hay una solución fácil para esto sin cerrar el servicio antes de iniciar IsardVDI. Sucede que *chromium* en Linux comienza a escuchar el servicio *mDNS* en el puerto 5353. Esto se puede resolver fácilmente usando otro navegador como *firefox* para acceder a IsardVDI o iniciar contenedores de IsardVDI antes de abrir *navegador chromium*. Tenga en cuenta que esto ocurrirá solo si va a utilizar el mismo dispositivo para ejecutar IsardVDI y acceder a el a un navegador local en el servidor.

## After doing the wizard the hypervisor details say there is no virtualization available

Algunas CPU (en su mayoría antiguas) no tienen virtualización de hardware, otras la tienen pero está desactivada en la BIOS. En el primer caso no hay nada que se pueda hacer. Si está deshabilitado en la BIOS, debe verificar VT-X o la Virtualización o SVM y activarlo.

[Más información en admin faqs](../admin/faq.md#after-finishing-install-the-default-isard-hypervisor-is-disabled)

# Instalación anidada en KVM

Verifique la opción de virtualización anidada en su sistema operativo host:

- Procesadores Intel: ```cat /sys/module/kvm_intel/parameters/nested```
- Procesadores AMD: ```cat /sys/module/kvm_amd/parameters/nested```

Debería mostrar un ** 1 ** o ** Y ** si está habilitado.


94/5000
Deberá habilitar la virtualización anidada en su sistema operativo host si aún no está activo.

## Virt anidados en procesadores Intel::

### Live

Con todas las máquinas virtuales detenidas eliminar
kvm_intel module
```
modprobe -r kvm_intel
```

37/5000
Y vuelve a cargarlo con opción anidada:
```
modprobe kvm_intel nested=1
```

### Permanent

Crea la carpeta ```/etc/modprobe.d/kvm.conf``` y añade:
```
options kvm_intel nested=1
```

## Virt  anidados en procesadores AMD::

### Live

Con todas las máquinas virtuales detenidas elimina
kvm_amd module
```
modprobe -r kvm_amd
```
Y vuelve a cargarlo con opción anidada:
```
modprobe kvm_amd nested=1
```

### Permanent

Crea la carpeta ```/etc/modprobe.d/kvm.conf```  y añade:
```
options kvm_amd nested=1
```

# Instalación  de IsardVDI dentro de VMWare ESXi guest

Habilitar el paso de la CPU del host al invitado. Eso debería bastar.
