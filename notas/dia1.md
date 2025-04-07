# Ansible

- Creación playbooks (cómo organizarlos internamente v.1)
- Inventarios (ini, yaml, carpeta... inventarios dinámicos)
- Facts / Custom facts
- Variables
- Introducción a Tower (AWX)
  - Crear organización / Grupo / Usuarios
  - Crear un proyecto
  - Crear un inventario
  - Crear un template
  - Crear un job

# En esta nueva formación

- Profundizar en la creación de playbooks
  - Variables/Estructuración de datos.
  - Estructuración de playbooks (Colecciones/Roles/Modules).
  - Flujos más complejos.
- Inventarios dinámicos.
- Ansible Tower
  - Workflows de playbooks.
  - Entornos de ejecución de los playbooks (contenedores).
- Git
- Automation Hub
- Ansible Galaxy
- Event Driven Ansible
- Ansible Navigator
  - Ansible Lint

---

## Ansible. Qué tipo de lenguajes se usa en Ansible (Playbooks)?

- Se usa a primer nivel... en los playbooks: Imperativo
- En las tareas... al dar instrucciones a un módulo: Declarativo (en la mayoría de los casos) -> Idempotencia

### Idempotencia

Es la característica por la cuál se nos asegura que el resultado de una operación no depende del estado inicial en el que se encuentre el entorno donde se lleva a cabo esa operación. Independientemente del estado inicial, siempre llegamos al mismo estado final.
De hecho en muchos de los módulos de Ansible, existe una propiedad que configuramos llamada: `state`, que hace alusión precisamente a ese estado final.

La idempotencia es una característica que tienen muchos módulos de Ansible (no todos):
shell

Los playbooks no me garantizan la idempotencia. Puedo conseguirla pero es algo a lo que tengo que prestar atención. Necesito tener en cuenta los distintos posibles estados iniciales... para asegurarme que siempre llego al mismo estado final a nivel de playbook.

# DEVOPS

Cultura, movimiento en pro de la automatización de qué? de todo lo que pueda y tenga sentido entre el dev --> ops.

En devops tenemos en cuenta el ciclo completo de vida del software.

Dónde entra ANSIBLE? dentro del ciclo de vida de un producto de software? En que etapas?
- Desarrollo? NO
- Test, Deployment, Operate, Monitor? SÍ

En general, cada vez que necesitemos configurar un entorno.

Esto tiene una implicación... sobre todo si tenemos en cuenta que hoy en día usamos lo que se llaman arquitecturas de desarrollo (de un playbook/varios)de software de componentes desacoplados.

Con Ansible lo que estamos haciendo es crear SCRIPTS (automatización).
Un script no es sino un tipo de software (programa)... y tiene que someterse a los mismos principios de desarrollo que el resto del software que hacemos (oracle database):
- control de versiones
- arquitecturas 
- patrones de desarrollo
- Pruebas
- documentación
- etc.

## Sistema / Aplicación monolítica?

Es cuando diseño un sistema en el que todas las piezas están fuertemente acopladas entre si... y funciona muy bien... el problema es que si cambio algo en cualquier parte del sistema, tendrá un impacto importantísimo en el resto del sistema.

Además, me será complicado reusar cualquier parte del sistema en otro sistema diferente.

Llevado a nuestro caso:
- Sería crear un megaplaybook que haga todo.

Una alternativa a esto es crear un sistema de componentes desacoplados entre sí. Cada componente tiene su propia lógica, y su propio ciclo de vida... también su propia API:

## Sistema / Aplicación de componentes desacoplados?

Es cuando diseño un sistema en el que todas las piezas están débilmente acopladas entre si... (API). La gracia de este tipo de arquitectura:
- es que si cambio algo en cualquier parte del sistema, tendrá un impacto mínimo en el resto del sistema.
- podré reusar cualquier parte del sistema en otro sistema diferente.

Llevado a nuestro caso:
- Montar montónones de playbooks (muy sencillos) en lugar de un megaplaybook.

Eso me permitirá reusar los playbooks en otros sistemas/procesos diferentes, y además me permitirá hacer un desarrollo más ágil de los playbooks (siempre que usemos un sistema de control de versiones, claro).

### Automatizar

Es montar una máquina/programa(que cambie el compartamiento de una máquina) para que haga algo (un trabajo) que antes hacía un humano:
- Lavadora: máquina que lava la ropa por mi... ya no rasco yo en una tabla.
  La lavadora incluso tiene 'PROGRAMAS', que me permiten variar el comportamiento de la máquina dependiendo de lo que quiera hacer (lavar, centrifugar, aclarar, etc.)

El hecho de tener una lavadora, implica que ya no haga falta un humano?
- Hay que cargarla
- Hay que darle a play
- Hay que elegir el programa de funcionamiento

Cuando en el mundo IT nos vamos a una cultura DEVOPS... queremos automatizar TODO lo que pueda automatizar. Es decir, quiero minimizar al máximo la presencia/intervención humana.
Si puedo hacer que desaparezca la intervención humana, mejor que mejor.

Esto implica que querré montar muchos tipos de automatizaciones... y además, que esas automatizaciones ocurrirán a distintos niveles.
- PRIMER NIVEL DE AUTOMATIZACIÓN PUEDE SER CREAR UNA MAQUINA PARA LAVAR LA ROPA
  Pero sigue haciendo falta un humano que le dé al play y elija el programa de funcionamiento. 
- La cosa, es que teniendo ya esa máquina, puedo montar otra máquina/programa que haga que la lavadora se ponga en marcha a una hora determinada automáticamente (si hay ropa dentro), y que elija el programa de funcionamiento dependiendo del tipo de ropa que haya dentro (si es blanca, si es de color, etc.) 
  ESTE SEGUNDO NIVEL DE AUTOMATIZACIÓN, solo es posible si tengo la primera máquina (lavadora) funcionando.

Vamos a hacer playbooks...
Cuándo se van a ejecutar esos playbooks? Bajo qué condiciones?
- Cuando yo lo ejecute manualmente... pero esto es lo que queremos? no.
  Esto será un primer paso... (como crear una lavadora) (COHETE!!!!!)
  Hemos dicho que queremos que desaparezca la intervención humana... y eso implica que los playbooks se ejecuten automáticamente... cuándo?
- Programando las ejecuciones de los playbooks (cron) 
  - Esto nos lo permite hacer Ansible Automation Platform (Tower/AWX)
     Los jueves primeros de mes a las 13 horas.. siempre que caiga en luna llena!
    Lo que pasa es que este escenario... es un poco cutre!
    Quiero decir... para algunos playbooks (automatizaciones) puede tener sentido... pero para otros no.

    Cuándo quiero regenerar los certificados de un servidor? 
    - Cuando vayan a caducar.
    - Cuando despliego un servidor nuevo.
    - Cuando se requiere un cambio en el certificado.
    - Cuando el certificado se ha visto vulnerado.
    - En general NPI!
    Habrá cosas/momentos fáciles de identificar... pero otras no tanto. 

    Aquí es donde va a salir un concepto: EVENT DRIVEN AUTOMATION
    Queremos que la ejecución de los playbooks se dispare por un evento EXTERNO (fuera de Ansible)... o interno (dentro de Ansible, como por ejemplo la previa ejecución de otro playbook que haya dado un error/o que haya ido bien... npi).

    El punto de esto es que cuando creo un playbook, normalmente lo creo para un escenario de uso que tengo en la cabeza... pero eso no significa que el playbook solo vaya a usar para ese escenario de uso. Realmente no tengo NPI de cuándo alguien / ALGO va a necesitar ejecutar ese playbook.

    Esto implica que debo tener especial cuidado con el concepto de IDEMPOTENCIA!
    Si diseño un playbook de esta forma (RESPETANDO IDEMPOTENCIA), podrán usar mi playbook cuando les dé la gana... y no pasará nada.
    Si diseño un playbook que no respete la idempotencia... puede que el playbook funcione perfectamente en el escenario de uso que yo tenía en mente... pero si alguien lo ejecuta en otro escenario de uso... puede que no funcione como se espera.

Volvamos al ejemplo de los certificados.
Cuándo quiero (re)generar los certificados de un servidor? 
- Cuando vayan a caducar.
- Cuando despliego un servidor nuevo.
- Cuando se requiere un cambio en el certificado.
- Cuando el certificado se ha visto vulnerado.
- En general NPI!

En muchos de esos casos (de arriba), la (re)generación de mis certificados será parte de un trabajo automatizado de orden superior (de más alto nivel):
- PROCESO 1 : Añadir un servidor nuevo de nginx al cluster del entorno de producción
    1. Lo primero es tener / crear ese servidor (host/vm/container)
       Quién me puede ayudar con esto? 
       - Terraform / CloudFormation (si lo que quiero es un host/vm)
       - Docker/Kubernetes (si lo que quiero es un contenedor)
       - Ansible (también podría... aunque no es su fuerte.. y por ende no es habitual)
    2. Configurar el entorno previamente (proxy) / crear usuarios / preparar carpetas
    3. El nginx tendrá unas dependencias
    4. Instalar el nginx
    5. Configurar el nginx
    6. Generar los certificados del nginx
    7. Montaré herramientas de monitorización a nivel de host/servicio
    8. MONTÓN DE COSAS

    Quién coordinará todo esto? Workflow... en el Ansible Automation Platform (Tower/AWX)?
    Si solo trabajo con Ansible puede... pero no es habitual... tendré ansible y otras n herramientas... y ahñi no llega el AUTOMATION PLATFORM.
    Aquí tiro de servidores de automatización:
    - Jenkins
    -> Terraform
    -> Ansible Automation Platform API (sería pedirle a Jenkins que cuando toque apriete en cohete en el TOWER)
    - Gitlab CI/CD
    - RunDeck

- PROCESO 2: Una paso autocurativo en un proceso de monitorización
  - Llamo al nginx cada x tiempo
  - Si falla... empiezo a revisar cosas.
    - Entre ellas, pido la regeneración de los certificados del nginx
  

A la hora de implementar el proceso 1, posiblemente de la tarea 2-8 las haga con Ansible.
Eso implica que podría montar un único playbook que haga todo eso.
Lo que pasa es que ese playbook iba a quedar con 100 tareas. Quiero eso? NO
- Sería in-mantenible

Opciones que se nos ocurren, para favorecer la mantenibilidad y reusabilidad de las tareas?
- Para el tema de los certificados podría crear un ROLE. Es lo que querría en este caso?
  Sería una buena solución? NO SOLO... Podría montar un ROLE si quiero... pero no es suficiente ni de coña! Ni óptimo. De hecho puede ser hasta innecesario (sobreingeniería).
- Podría crear un playbook que haga solo eso: (re)generar certificados.
  - El tener un ROLE ... UN ROLE SE PUEDE EJECUTAR? No
  - Yo lo que necesito es algo que pueda EJECUTAR cuando sea necesario:
    - Como parte del proceso 1, como parte del proceso 4, como parte del proceso n (el de monitorización)
  -  Si lo que defino es un ROLE (O una collection que tenga ese ROL...) necesitaré en cada proceso usar ese ROLE (o esa collection) en un playbook.
  -  Que hago monto n playbooks, para los n procesos? VAYA RUINA !
     La mantenibilidad de eso es muy baja. Como haya un cambio en la forma de generar los certificados... tendré que cambiar n playbooks.
  - Lo que me interesa es un PLAYBOOK (EJECUTABLE) único, que haga la (re)generación de los certificados. Ese playbook podrá tirar de un ROLE o no, según me interese...
    Puede ser que esa funcionalidad la vaya a usar en otros playbooks? o no.
    Quizás lo que si necesito es una funcionalidad de más bajo nivel que sea GENERAR UN CERTIFICADO.. esto tendría sentido meterlo en un ROLE.
    Pero ese certificado tendrá luego que montarlo/configurarlo en el nginx... y eso es una tarea que a priori parece muy específica (como para querer generificarla... y llevarla a un ROLE)

Lo que me va a tocar es coordinar playbooks entre si:
- Primero ejecuta el playbook que configura una máquina para que salga a internet (proxy)
- Luego ejecuta el playbook que instala el nginx
- Luego ejecuta el playbook que configura el nginx
- Luego ejecuta el playbook que (RE)GENERA los certificados del nginx <<< QUIERO QUE SEA UN PLAYBOOK
- Y luego otros...

Esta funcionalidad me la ofrece el TOWER (Automation Platform) a través de los WORKFLOWS.

## Qué es un ROLE?

Un paquete de tareas reutilizables, que se pueden usar en distintos playbooks.
Los roles en Ansible se crean siguiendo una determinada estructura de carpetas y ficheros.

Em los lenguajes de programación tradicionales, un ROLE equivale a una función / método / subrutina de una librería.

La librería en Ansible tendría su equivalente en las COLECCIONES.

Una COLLECTION SE PUEDE EJECUTAR? NO




---

# Vamos a montar un primer ejemplo.

Muchas veces necesitamos una máquina virtual nueva... para lo que sea.

Una vez que tenga esa VM, le montaré las cosas que necesite... y la configuraré como sea necesario <- ANSIBLE

Pero antes de eso... y una vez creada la máquina virtual... lo que tengo es que poder conectarme a esa máquina virtual desde ansible.

Imaginemos que mi vm es GNU/LINUX... cómo se hace esa conexión? SSH

SSH lo puedo usar de 2 formas (de cara a la autenticación):
- Con usuario y contraseña
- Con clave pública/privada

Pero esas formas, se diferencian en otra cosa.

Ahí arriba hablaba yo de AUTENTICACIÓN... Autenticación de quién?
- Quién se conecte con el servidor? SI... pero no solo.
- El host con el que yo me conecto (yo=cliente) también tengo que autenticarlo.
  A ver si resulta que me han dado gato por liebre... y estoy conectandome a un servidor pirata... que me lo han cambiado... y le estoy pasando mi contraseña (phishing = suplantación de identidad).

En los 2 escenarios de arriba hay que hacer esa autenticación del servidor?
- Solo cuando me conecto con usuario y contraseña.
  Antes de darle al servidor mi contraseña, me tengo que asegurar que el servidor al que me estoy conectando es el servidor al que quiero conectarme.
  Para eso veo si el servidor lo tengo registrado en mi máquina (en el fichero ~/.ssh/known_hosts) y si la huella digital del servidor coincide con la que tengo registrada.
  El servidor genera una clave que lo identifica... en la que incluye su mac address y su ip... y otra información.
  Esa clave la guardo yo en mi máquina (en el fichero ~/.ssh/known_hosts) la primera vez que me conecto con el servidor. A partir de ahí, cada vez que me reconecto, el servidor me vuelve a enviar esa clave... y yo la comparo con la que tengo guardada en mi máquina.
  Si no coincide... no conecto... CUIDAO !!!!! Y tomo decisiones: SIGO ADELANTE O NO!
    Esto es similar a cuando el navegador de internet me dice: EL CERTIFICADO NO CORRESPONDE AL NOMBRE DEL SERVIDOR. CUIDADO! Quieres seguir o no?
- Cuando me conecto con clave pública/privada... Comparto mi clave pública copiándola dentro de un fichero que hay en el servidor: authorized_keys. Ese dato es sensible? NO... no tiene nada que me comprometa (por eso la llamamos pública).
  Y lo de la clave privada? la clave privada la tengo yo.

  Si el servidor tiene mi clave dentro... será que es un servidor de confianza... donde he puedo yo esa clave... y no necesito autenticarle.

Cuando me conecto con contraseña, la primera vez, se genera esa clave/token que se incluirá dentro de mi fichero .ssh/known_hosts. 
Qué pasa si intento hacer eso desde ansible? y nunca me he conectado con ese host?
- Me pide en medio de la ejecución que establezca MANUALMENTE si quiero confiar en ese servidor.... pausando la ejecución del playbook.
  Y yo voy a estar ahí para escribir YES? Evidentemente no... si no VAYA MIERDULI DE AUTOMATIZACION.
- Y esto es habitual? este escenario? O en el entorno donde ejecute Ansible tendré ya la clave del servidor?
  Dónde se ejecuta el playbook en última instancia... en el entorno real de trabajo?
  - En el servidor donde tengo ansible instalado? NO
  - En el servidor del inventario? NI DE COÑA... eso si que nunca... en ese servidor se ejecutan los módulos del playbook... pero no el playbook en sí.
  - El playbook se ejecutará en un contenedor que va a abrir el Tower (AWX) para ejecutar el playbook. 
    Y qué pasa con el contenedor una vez ejecutado el playbook? A la basura.. y su fichero kwown_hosts? A la basura también.

Lo normal es que ansible se conecte a los servidores de destino (del inventario) a través de SSH haciendo uso de claves públicas/privadas. ESTO ES LO QUE QUIERO! SIEMPRE
Menos la primera vez.
Cuando creo un servidor nuevo en VMWare... en un cloud... lo creo desde una plantilla. Y en esa plantilla no tengo mi clave ssh... de hecho esa clave va cambiando con el tiempo.
(NOTA: Tendré en mi ecosistema Ansible un playbook que vaya rotando las claves ssh de los servidores de mi inventario... y que se ejecute cada x tiempo... o bajo demanda)

Lo más normal es que la plantilla tenga un usuario root... y una contraseña temporal.
Y nada más crear el servidor, me conecto con ese usuario y contraseña... y le copio mi clave pública al servidor (al fichero authorized_keys), restablezco la contraseña del root (que también la voy regenerando cada x tiempo...) y desactivo la autenticación por contraseña para ssh.

Vamos... que la primera vez que me conecte voy a tener folclore...
En ansible puedo decir, pasa del tema! (Tan fácil como poner una variable en el inventario: ansible_ssh_common_args='-o StrictHostKeyChecking=no' o una variable de entorno: ANSIBLE_HOST_KEY_CHECKING=False)

Pero dejo el sistema muy vulnerable.

Vamos a montar un playbook, cuya misión es dar de alta o no... o modificar mi archivo known_hosts (el de la máquina/entorno donde se ejecute el playbook) con la clave del servidor al que me quiero conectar.

De hecho, posteriormente lo vamos a convertir en un ROLE... y lo vamos a usar en otros playbooks.

- Si teneis máquina Linux. nada , sin problema.
- Si teneis máquina Mac -> docker/podman
- Si teneis máquina windows
  -> Docker/Podman Desktop (con WSL2 - Características de windows)
        Tienda de apps -> Ubuntu
  -> VirtualBox -> Montar un linux (ubuntu desktop) 

# Seguridad

Identificación          Decir quien soy
Autenticación           Demostrar que soy quien digo ser
Autorización            Sabiendo que eres quien dices ser, determinar si puedes hacer algo o no.

# Conexión por SSH contra los hosts de Ansible

Ansible conecta por ssh con los host cuando va a ejecutar una tarea que requiere ejecutarse dentro del host.
Esa conexión ssh se cierra en cuanto la tarea ha terminado. Las conexiones son independientes entre tareas.

De hecho a veces podemos tener problemas al ejecutar un playbook si una tarea tarda mucho en ejecutarse.
Puede ser que ssh corte la conexión a medias!

Ansible permite ejecutar las tareas de forma síncrona o asíncrona!
- Síncrona:     Ansible mantiene la conexión ssh abierta hasta que la tarea ha terminado.
                Y espera a que la tarea termine para continuar con la siguiente tarea.
                Las tareas se ejecutan secuencialmente.
                Es el comportamiento de ansible por defecto.
- Asíncrona     Ansible lanza desde una conexión ssh la tarea en segundo plano
                Y cierra la conexión ssh.
                Y no espera a que la tarea acabe para empezar con la siguiente.
                Esto me permite ejecutar un montón de tareas simultáneamente dentro de un host remoto.
                Eso lo hacemos con la propiedad async. Esa propiedad se establece a nivel de cada tarea
```yaml
-   name: Mi tarea
    command: /usr/bin/mi_tarea
    async: 10                       # TIEMPO MAXIMO QUE DAMOS A LA TAREA
    poll: 0                         # Cada cuanto tiempo compruebo si la tarea ha terminado
    register: mi_tarea

-   name: Esperar a que la tarea termine
    async_status:
        jid: "{{ mi_tarea.ansible_job_id }}"
    register: job_result
    until: job_result.finished
    retries: 10
    delay: 5

``` 
                Si configuro un pool de 0, ansible no espera a que la tarea acabe... y es cuando pasa a las siguientes tareas, dejando la anterior ejecutándose en paralelo (en segundo plano)

                Lo que puede pasar en este caso., e que en algún momento quiera asegurarme qde que l tarea acabó, para seguir haciendo otras cosas... o simplemente para dar el playbook por bueno.
                Entonces tenemos otros módulos que nos permiten comprobar el estado de la tarea.
                - ~~wait_for (Para comprobar si un puerto está abierto)~~
                - async_status (Para comprobar el estado de una tarea asíncrona)

```yaml
- name: Mi tarea
  command: /usr/bin/mi_tarea
  async: 10                       # TIEMPO MAXIMO QUE DAMOS A LA TAREA
  poll: 2                         # Cada cuanto tiempo compruebo si la tarea ha terminado
``` 

La tarea se ejecuta como cualquier otra tarea. Y no avanza a la siguiente hasta que ésta no ha terminado,
solo que no mantiene abierta la conexión ssh todo el tiempo. Ca
da 2 segundos se conecta a ver si acabó o no.Si ha
y un error, lo veo en el mismo sitio que vería el error de cualquier otra tarea o de ella misma si la hubiera ejecutado síncrona.

```yaml
-   name: Mi tarea
    command: /usr/bin/mi_tarea
    async: 10                       # TIEMPO MAXIMO QUE DAMOS A LA TAREA
    poll: 0                         # Cada cuanto tiempo compruebo si la tarea ha terminado
    register: mi_tarea

# ^^^ Esa tarea en caso de haber un problema al lanzar la ejecución si da error... como siempre
# ^^^ Esa tarea en caso de haber un problema durante su ejecución no va a dar error.

- debug: 
    msg: Estoy haciendo algo en el playbook en paralelo a la tarea

-   name: Esperar a que la tarea termine
    async_status:
        jid: "{{ mi_tarea.ansible_job_id }}"
    register: job_result
    until: job_result.finished
    retries: 10
    delay: 5

# ^^^ Aquí es donde voy a mirar qué pasó con la tarea anterior.
#     Y aquí es donde puedo tener error
``` 
