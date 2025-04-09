
Queremos un playbook para la gestión/generación de pares de claves ssh para poder usarlas luego contra nuestros hosts desde ansible.

Queremos que el playbook genere claves ssh y las instale en un servidor (vamos... que copie la publica dentro del archivo authorized keys)?

    OPCION 1

        CLIENTE --- ssh ------> SERVIDOR
                                usuario/contraseña
        paso el usuario y password existente en servidor

    OPCION 2

        CLIENTE 1 --- ssh ------> SERVIDOR 1
                                usuario(contraseña)
                                    authorized_keys <---- clave pública
        clave_privada

        CLIENTE 2 --- ssh ------> SERVIDOR 2
                                usuario(contraseña)
                                    authorized_keys <---- clave pública
        clave_privada


    Lo normal en una infraestructura es que no tenga un par de claves ssh para cada servidor. Puedo tenerlas y me da extra-seguridad
    Lo más habitual es que yo (HUMANO/PROGRAMA) tenga una clave privada y su pública la copio en 300 servidores.

    ANSIBLE AWX 
        Gestor de claves (VAULT)
            - clave ssh privada PRODUCCION
            - clave ssh privada PRODUCCION BBDD
            - clave ssh privada DESARROLLO
            - clave ssh privada PRUEBAS

Playbook 1: Generación de claves / Asegurar la existencia de ciertas claves ssh
Playbook 2: Copia de claves publicas a unos servidores remotos

En ocasiones ejecutaré solo el playbook 2
En ocasiones ejecutaré el playbook 1 y el playbook 2
En ocasiones ejecutaré solo el playbook 1 (para generar unas claves que quiero registrar en gitlab-cosa que hago con un playbook3)

Vamos a NO crear un MEGA PLAYBOOK que haga de todo y mejor creemos 300 playbooks que hagan cosas muy concretas.
Será como piezas de un lego... Luego uniré los que necesite en cada momento (para cada tipo de trabajo) : WORKFLOWS DE AWX/TOWER

---

Playbook 1: Generación de claves / Asegurar la existencia de ciertas claves ssh

VARIABLES
- algoritmo: rsa, dsa, ecdsa, ed25519
               v 
- tamaño rsa: 2048, 4096
- ecdsa: 256, 384, 521
- ed25519: 

passphrase

Lo que genera el playbook son 2 ficheros:
- id_rsa
- id_rsa.pub

Cómo llamo a esos ficheros?
Donde los guardo?

A lo mejor las claves ya existen... pero quiero regenerarlas.
Por defecto si una clave existe, no quiero que se regenere... a no se que me digan lo contrario.

Quiero que se genere solo 1 par de claves por ejecución o que se me generen 200 pares de claves de una?

Necesito que las claves se van generando de 1 en 1(secuencialmente)? o si hay que generar 200 pares de claves, se generen todas a la vez (en paralelo)? paralelo