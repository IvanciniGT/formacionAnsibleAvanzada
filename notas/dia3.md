# Contenedor?

Para trabajar con contenedores necesitamos un gestor de contenedores: Docker, Podman(redhat), Crio, Containerd...

Entorno aislado donde ejecutar procesos dentro de un host con sistema operativo LINUX!
Aislado?
- Ese entorno tiene su propio FileSystem
- Ese entorno tiene sus propias variables de entorno
- Ese entorno tiene su propia configuración de red -> su propia IP
- Ese entorno puede tener limitaciones de recursos (memoria, CPU, disco)

Cómo se crea uno un contenedor?
- Partimos de una imagen de contenedor. Se descomprime y sirve de base para el sistema de archivos del contenedor


# Imagen de contenedor?

Un triste fichero comprimido (tar) que contiene:
- Una estructura de directorios (normalmente compatible con POSIX)
    /
        opt/
        bin/
        lib/
        lib64/
        etc/
        var/
        usr/
        home/
        tmp/
- Y programas ya instalados en esas carpetas... Decenas... o cientos.
        /bin/ls
        /bin/cat
        /bin/sh
        /bin/more
        /bin/wget
        /bin/yum

Las imagenes de sontenedor se guardan en REGISTROS DE REPOSITORIOS DE IMAGENES DE CONTENEDOR.
El más famoso:
- docker hub
- quay.io:     Este es el de redhat
- microsoft container registry
- oracle container registry

Las empresas suelen montar sus propios registries privados ... para sus propias imagenes de contenedor:
- gitlab registry
- nexus
- artifactory
- quay.io


# Que ventajas nos ofrece el trabajar con contenedores?

- SEGURIDAD: Los procesos que corren dentro de un contenedor no pueden acceder a los procesos que corren en el host.. o a los archivos del host
- CONTROL SOBRE RECURSOS: Puedo limitar la cantidad de recursos que puede usar un contenedor (memoria, CPU, disco)
- Al partir de una imagen de contenedor, si la han creado bien, tengo un entorno de trabajo que es portable y reproducible con TODO LO QUE NECESITO para trabajar

# Volumenes en contenedores

- Los contenedores ofrecen persistencia de los datos/archivos/directorios? La misma que mee dan las VMs... o los host físicos...
  Si un contenedor lo paro y lo arranco luego... tengo los mismos datos que tenía antes? SI
  Si una VM la paro y la arranco luego... tengo los mismos datos que tenía antes? SI
  Si un host físico lo apago y lo enciendo luego... tengo los mismos datos que tenía antes? SI

  Si una VM la borro, y luego creo otra con el mismo nombre... tengo los mismos datos? NO
  Si una host físico lo borro, y luego creo otro con el mismo nombre... tengo los mismos datos? NO
  Si un contenedor lo borro, y luego creo otro con el mismo nombre... tengo los mismos datos? NO

  Los contenedores SI OFRECEN PERSISTENCIA... la misma que las VMs y los hosts.

  El problema es que los contenedores los creamos y borramos con mucha alegría!

## Qué es un volumen en un contenedor:

- Un punto de montaje en el sistema de archivos del contenedor que apunta a un almacenamiento (físico o virtual) que se encuentra fuera del sistema de archivos del contenedor.
    Lo mismo que hago en un host Windows, cuando monto una CARPETA DE RED
    Lo mismo que hago en una VM cuando monto una carpeta compartida con el host
    Lo mismo que hago een un servidor cuando hago un MOUNT de un servidor nfs

## Para qué sirven al trabajar con contenedores?

- Para compartir datos entre el contenedor y el host (poco habitual... al menos así contado)
  Para inyectar desde el host archivos y carpetas al contenedor (SI) 
- Para compartir datos entre contenedores
- Para persistir datos tras la muerte (eliminación) de un contenedor

---

En AWX / AutomationPlatform (Antiguo Tower), los playbooks al final se ejecutan en CONTENEDORES. Por qué?
- Porque el contenedor tiene todo lo que necesita el playbook para ejecutarse (python, ansible, etc)
- Porque si jode algo (borra archivos, etc) no afecta al host, ni a otros trabajos que se estén ejecutando en otros contenedores
- Me ofrece entornos reproducibles. Me aseguro que si tengo un entorno de desa, uno de pre y uno de prod, todos tienen la misma versión de ansible, python, etc

Desde qué imagen se crea ese contenedor?
- Antiguamente, en una fija que daba redhat... que tenia preinstalado ANSIBLE.
- Hoy en día, podemos elegir con que imagen de contenedor trabajar: ENTORNOS DE EJECUCION DE ANSIBLE
  Para qué querría yo hacer esto???
    Crearme mi propia imagen de contenedor... con ansible, si ya hay una oficial?
       - En la imagen oficial de ansible, van a venir predescargados los ROLES CUSTOM que he creado en mi organización y mi playbook necesita?
       - En la imagen oficial de ansible, van a venir predescargados los PLUGINS/MODULOS CUSTOM que he creado en mi organización y mi playbook necesita?
       - En la imagen oficial de ansible, van a venir predescargados las COLLECTIONS CUSTOM que he creado en mi organización y mi playbook necesita?
       Nada de nada.
       Y lo que hacemos es crearnos nuestras propias imagenes de contenedor. PARTIENDO DE LA IMAGEN OFICIAL DE ENTORNO DE EJECUCION QUE OFRECE REDHAT

Cómo me aseguro de que una imagen de contenedor que he creado está bien para ejecutar un playbook?
Es más... como me aseguro de que mi playbook que estoy desarrollando se ejecuta bien sobre una determinada imagen de contenedor que yo también he creado?

Eso (el ejecutar playbooks dentro de contenedores) es lo que hace AWX / AutomationPlatform (Tower)...
Como pruebo yo? Me creo una imagen, la subo a algún sitio, me creo un contenedor, ejecuto allí dentro mi playbook? Pues si que tengo que hacer cosas... para asegurarme que mi playbook luego va a funcionar.

Una cosa es en mi máquina funcione!!!
Puede ser que en mi máquina tenga una determinada configuración, versión de ansible... roles descargados... que hagan que aquello funcione... Pero luego en el tower va a funcionar??  Ah!!!!
Para evitarme sorpresas uso contenedores. ES LA FORMA UNICA OFICIAL HOY EN DIA DE TRABAJAR CON ANSIBLE

Y para poder trabajar localmente (desarrollo) con playbooks, me han creado una herramienta que me facilite la ejecución de playbooks dentro de contenedores:

# Ansible-Navigator


---
