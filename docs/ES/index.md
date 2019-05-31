# Isard**VDI**

Es un controlador de escritorios virtuales de código abierto basado en KVM Linux y dockers.


## Inicio Rápido

Obten el archivo docker-compose y crealo:

```
wget https://raw.githubusercontent.com/isard-vdi/isard/master/docker-compose.yml
docker-compose pull
docker-compose up -d
```

Ve a https://localhost y sigue el asistente. Ahora estara listo para usar escritorios virtuales:


![](images/install.gif)

- Inicia **demo de escritorios**, conectate con el navegador y el spice o con un protocolo vnc.
No es necesario instalar nada, ya se han asegurado los certificados.
- instala virt-viewer y conectate al cliente usande el spice. **Sound 
and USB** estará disponible.

Descarga nuevos escritorios, isos y mas recursos desde el menu de **Updates**.

Crea  tus propios escritorios usando las isos descargadas  desde Updates o **Media** en el menu de opciones. Cuando termine de instalar el sistema operativo y las aplicaciones crea un **Template** y decide qué usuarios o categorías pueden crear un escritorio idéntico a esa plantilla. Gracias a al **incremental disk creation**, todo esto se puede hacer en minutos.


<iframe src="https://drive.google.com/file/d/1tPL12yw3MEV5IEPL5by7z76zVVSNnAng/preview" width="640" height="480"></iframe>

No se limite a una instalación "independiente" en un servidor. Puedes agregar más hipervisores a tu **pool** y dejar que IsardVDI decida dónde iniciar cada escritorio.
Cada hipervisor solo necesita los paquetes KVM / qemu y libvirt
y un acceso SSH. Debes mantener el almacenamiento compartido entre
esos hipervisores.

Actualmente gestionamos una **gran infraestructura IsardVDI** en la Escola del
Treball de barcelona. 3K estudiantes y profesores tienen IsardVDI disponible from our self-made pacemaker dual nas clúster y seis hipervisores, 
Que van desde las placas base de doble núcleo del servidor Intel de nivel superior hasta gigabyte 
gaming ones. 


Tenemos experiencia en diferentes **clientes ligeros** que usamos para reducir la renovación y
Costes de consumo en las aulas.

[IsardVDI Project website](http://www.isardvdi.com/)

### Authors
+ Josep Maria Viñolas Auquer
+ Alberto Larraz Dalmases

### Contributors
+ Daniel Criado Casas
+ Néfix Estrada

### Support/Contact
Envíenos un correo electrónico a info@isardvdi.com si tiene alguna pregunta or fill in an issue.