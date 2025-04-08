ansible-navigator:

Es un comando que os ofrece utilidades para la gestión de:
- playbooks
  - ejecutar
  - lint
- inventarios
- roles
- collections

Esto necesita por debajo tener instalado ansible.

Ansible se puede instalar en windows? NO

Por eso necesito:
- Windows: WLS: Máquina virtual Linux
    ansible-navigator
  docker-desktop -> los contenedores los ejecuta en linux (WLS)
    - ansible
- MacOS: Docker
     ansible-navigator
     docker -> ansible
- Linux: Directamente instalo ansible y 
     ansible-navigator
     docker -> ansible

ansible-navigator necesita ansible... y ansible es el que tiene que estar instalado en LINUX
Pero la realidad es que ansible navigator NO necesita LINUX

Ansible-navigator lo que me permite es usar un entorno de ejecución de ansible en un contenedor linux