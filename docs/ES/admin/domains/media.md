<h1>Administer Media</h1>

[TOC]

# Upload media

Hay un botón **Add new** que abrirá un nuevo formulario para que IsardVDI descargue el contenido multimedia desde una URL:

- **URL**: Pega aquí cualquier URL de descarga de archivos ISO o floppy. (Los floppies  se pueden usar para agregar controladores de almacenamiento al instalar algunos sistemas operativos en el disco duro).
- **NAME**:Propondrá por defecto el nombre del archivo extraído de la URL. Cámbialo si prefieres otra.
- **TYPE**: Seleccione el tipo de medio que se está cargando (ISO/CDROM o Floppy)
- **DESCRIPTION**: Opcional
- **ALLOWS**:
79/5000
Se permiten roles, categorías, grupos y usuarios para usar este medio. Por favor refiérase a [allows form](../../user/allows.md#allows-form).

Cuando haga clic en el botón **Upload** se iniciará y verá el progreso.

## Carga de medios desde el almacenamiento local

Si tienes la ISO en un almacenamiento local y desas subirlo a IsardVDI, puedes crear un servidor web simple que pueda completar la url del archivo en el formulario de medios de carga en IsardVDI.

**Ejemplo de servidor web Python**

Si tienes el archivo *mycdrom.iso* en una carpeta, puedes iniciar un servidor web de Python:

```
$ ls .
mycdrom.iso
$ python -m SimpleHTTPServer
Serving HTTP on 0.0.0.0 port 8000 ...
```

La URL para descargar mycdrom.iso en los medios de carga IsardVDI será **http://localhost:8000/mcdrom.iso**

