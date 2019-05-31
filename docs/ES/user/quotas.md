<h1>Quotas</h1>

Las quotas pueden limitar los recursos utilizados por cada rol, categoría, grupo o usuario individualmente.

[TOC]

# Tipos de quotas

Hay un conjunto de quotas que se pueden modificar:

- **Desktops**: Número máximo de escritorios que se pueden crear.
- **Running:**: Número máximo de escritorios que el usuario puede tener ejecutándose al mismo tiempo.
- **Templates**: Número máximo de plantillas que el usuario puede crear a partir de sus escritorios.
- **Media** (o ISOs): número máximo de medios (ISOs/disquetes) que el usuario puede cargar en el sistema.
- **CPUs**:Número máximo de CPU virtuales que se pueden establecer en un escritorio.
- **Memory**: Cantidad máxima de memoria en MB que se puede ajustar a un escritorio.

# Quota selectivas para el usuario

Un usuario siempre se clasifica dentro de un rol, categoría y grupo, en ese orden de prioridad jerárquica. Esto significa que las cuotas efectivas para un usuario serán las más restrictivas de arriba hacia abajo.

Si modificamos la cuota para un usuario individualmente, esa cuota invalidará la que tenía antes en función de la jerarquía de roles, categorías y grupos.