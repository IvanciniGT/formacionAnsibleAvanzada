knownhosts
=========

Este rol se encarga de gestionar el archivo `known_hosts` de SSH del nodo donde corre ansible, asegurando que las claves públicas de los hosts conocidos estén presentes y actualizadas. 
Esto evita que ansible se detenga por problemas de autenticación al intentar conectarse a hosts remotos.

Además permite al usuario mucho control para determinar qué hosts se deben agregar o modificar, y qué algoritmos de firma se deben usar.

Tiene muchas otras opciones de configuración

Requirements
------------

Ninguno

Role Variables
--------------

```yaml

knownHostsFile:
    algorithm:                  ecdsa       #ed25519
    new:
        appendAutomatically:    true
        limitedTo:              ~               # Sea una lista, si quiero limitar, o null(undefined) si no quiero limitar.
        logHost:                true
        notifyChange:           true
        failHost:               true
    updated:
        updateAutomatically:    true
        limitedTo:              ~
        logHost:                true
        notifyChange:           true
        failHost:               true
```

Tabla explicativa:
| Variable                  | Descripción                                                                 | Valor por defecto |
|--------------------------|-----------------------------------------------------------------------------|---------------------|
| `knownHostsFile.algorithm`       | Algoritmo de firma a usar.                                                 | `ecdsa`          |
| `knownHostsFile.new.appendAutomatically` | Si se debe agregar automáticamente la clave pública al archivo `known_hosts` | `true`           |
| `knownHostsFile.new.limitedTo` | Lista de hosts a los que se les debe agregar la clave pública. Si es `null` se agrega a todos los hosts. | `null`          |
| `knownHostsFile.new.logHost` | Si se debe registrar el host al que se le agrega la clave pública. | `true`           |
| `knownHostsFile.new.notifyChange` | Si se debe notificar el cambio al host al que se le agrega la clave pública. | `true`           |
| `knownHostsFile.new.failHost` | Si se debe fallar el host al que se le agrega la clave pública. | `true`           |
| `knownHostsFile.updated.updateAutomatically` | Si se debe actualizar automáticamente la clave pública en el archivo `known_hosts` | `true`           |
| `knownHostsFile.updated.limitedTo` | Lista de hosts a los que se les debe actualizar la clave pública. Si es `null` se actualiza en todos los hosts. | `null`          |
| `knownHostsFile.updated.logHost` | Si se debe registrar el host al que se le actualiza la clave pública. | `true`           |
| `knownHostsFile.updated.notifyChange` | Si se debe notificar el cambio al host al que se le actualiza la clave pública. | `true`           |
| `knownHostsFile.updated.failHost` | Si se debe fallar el host al que se le actualiza la clave pública. | `true`           |

Dependencies
------------

Ninguna

Example Playbook
----------------

Puede usarse así:
```yaml

- hosts: localhost
  tasks:
  - name: Add host to known_hosts
    include_role:
      name: knownhosts
    vars:
      knownHostsFile:
        algorithm: ""
        new:
          appendAutomatically: true
          notifyChange: true
          failHost: false
        updated:
          notifyChange: true

  - name: Resto de tareas
    debug:
      msg: "El host se ha agregado al archivo known_hosts"

```

License
-------

BSD

Author Information
------------------

Esto lo he hecho yo, Iván, y si quieres me puedes contactar a través de mi correo electrónico: ivan@example.com
