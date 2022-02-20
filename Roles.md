DESCRIPCION DE LA ESTRUCTURA DE ROL
-----------------------------------

**REUTILIZACION DE GUIAS Y CODIGO**

PACKAGE rhel-system-roles  		<-- CONTIENE ROLES PREDEFINIDOS ANSIBLE GALAXY

ESTRUCTURA DEL ROL user.example.com (NO TODOS LOS ROLES TENDRAN TODOS LOS DIRECTORIOS)
````
user.example/
    ├── defaults
    │     └── main.yml			<-- VARIABLES DEFAULT DEL ROL, SE SUELEN OVERRIDEAR
    ├── files					<-- ARCHIVOS ESTATICOS USADOS POR EL ROL
    ├── handlers
    │     └── main.yml			<-- DEFINICIONES MANEJADOR DEL ROL
    ├── meta
    │     └── main.yml			<-- INFORMACION METADATOS dELR OL
    ├── README.md
    ├── tasks
    │     └── main.yml			<-- DEFINICION TAREAS DEL ROL
    ├── templates				<-- PLANTILLAS JINJA2 DEL ROL
    ├── tests
    │     ├── inventory			<-- INVENTARIO DEL ROL
    │     └── test.yml			<-- TEST DEL ROL
    └── vars
    	  └── main.yml			<-- VARIABLES DEL ROL (NO DEBERIAN TOCARSE, USO INTERNO)
````

**PRIORIDAD VARIABLES DEL ROLES**

    vars > inventario > defaults

**LLAMAR UN ROL DESDE UNA GUIA**

````
- hosts: remote.example.com
  roles:
    - role1
    - role2
````

**LLAMAR A UN ROL DEFINIENDO DOS VARIABLES**

````
- hosts: remote.example.com
  roles:
    - role: role1
    - role: role2
      var1: val1
      var2: val2
````	

>NOTA: SIEMPRE (mirar pre-tasks) SE EJECUTAN PRIMERO LOS ROLES, LUEGO EL RESTO DE TAREAS DE LA GUIA.
pre_tasks: SE EJECUTAN ANTES QUE LOS ROLES, TAMBIEN SE EJECUTAN ANTES LOS HANDLERS DE ESTAS TAREAS)
post_tasks: SE EJECUTAN DESPUES DEL RESTO DE TAREAS NORMALES DE LA REPRODUCCIONES

**ORDEN DE EJECUCION**

    pre_tasks ==> roles ==> tasks ==> post_tasks 

````
- name: Play to illustrate order of execution
  hosts: remote.example.com
  pre_tasks:
    - debug:
        msg: 'pre-task'
      notify: my handler
  roles:
    - role1
  tasks:
    - debug:
        msg: 'first task'
      notify: my handler
  post_tasks:
    - debug:
        msg: 'post-task'
      notify: my handler
  handlers:
    - name: my handler
      debug:
        msg: Running my handler
````

La tarea my handler se ejecuta tres veces:
* Después de que se hayan ejecutado todas las tareas de pre_tasks
* Después de que se hayan ejecutado todas las tareas de la sección tasks
* Después de que se hayan ejecutado todas las tareas de post_tasks

Los roles se pueden agregar a una reproducción con una tarea común:
* Use el módulo **include_role** para incluir dinámicamente un rol.
* Use el módulo **import_role** para importar estáticamente un rol.

**EJ. INCLUDE ROLE**

````
- name: Execute a role as a task
  hosts: remote.example.com
  tasks:
    - name: A normal task
      debug:
        msg: 'first task'
    - name: A task to include role2 here
      include_role: role2
````	

REUTILIZACION DE CONTENIDO CON ROLES DE SISTEMA
-----------------------------------------------

    NOMBRE                        DESCRIPCION DE ROL
    rhel-system-roles.kdump       Configura el servicio de recuperación ante fallos kdump.
    rhel-system-roles.network     Configura interfaces de red.
    rhel-system-roles.selinux     Configura y administra la personalización de SELinux, incluido el modo SELinux, 
                                  los contextos de archivos y puertos, la configuración booleana y los usuarios de SELinux.
    rhel-system-roles.timesync    Configura la sincronización temporal.
    rhel-system-roles.postfix     Configura cada host como un agente de transferencia de correo usando el servicio Postfix.
    rhel-system-roles.firewal     Configura el firewall de un host.
    rhel-system-roles.tuned       Configura el servicio tuned para sintonizar el rendimiento del sistema.

* **UBICACION ROLES**: /usr/share/ansible/roles
* **DOCUMENTACION DE ROLES**: /usr/share/doc/rhel-system-roles/<nombre_rol>/README.md
* SE PUEDE DEFINIR EN LA VARIABLE roles_path EN EL ARCHIVO DE CONFIGURACION DE ANSIBLE
* SE PUEDE DEFINIR UTILIZANDO LA VARIABLE DE ENTORNO ANSIBLE_ROLES_PATH


**EJEMPLO ROL SINCRONIZAR TIEMPOS (rhel-system-roles.timesync)**

````
- name: Time Synchronization Play
  hosts: servers
  vars:
    timesync_ntp_servers:
      - hostname: 0.rhel.pool.ntp.org   |
        iburst: yes                     |
      - hostname: 1.rhel.pool.ntp.org   | ---> VARIABLES DEL ROL
        iburst: yes                     | ---> PARA timesync_ntp_servers
      - hostname: 2.rhel.pool.ntp.org   |
        iburst: yes                     |
    timezone: UTC

  roles:
    - rhel-system-roles.timesync				<-- LISTA DE UN SOLO ELEMENTO

  tasks:
    - name: Set timezone
      timezone:
        name: "{{ timezone }}"
````        

**ESTRUCTURA DIRECTORIOS PARA timesync**
````
    .
    ├── ansible.cfg
    ├── group_vars
    │     └── servers
    │           └── timesync.yml		<-- DEFINE LAS VARIABLES DE SINCRONIZACION TEMPORAL PARA GRUPO SERVERS
    ├── inventory
    └── timesync_playbook.yml			<-- EL CONTENIDO DE LA GUIA UTILIZAR
````

**EJEMPLO ROL SELINUX (rhel-system-roles.selinux)**
>PARTICULARIDADES DEL ROL DE SELINUX
El rol establecerá una variable booleana, selinux_reboot_required=true y fallará si se necesita un reinicio. 
Puede usar una estructura block/rescue para recuperarse de la falla, ya sea que falle la reproducción porque la variable
no se configura en true, o que reinicie el host administrado y vuelva a ejecutar el rol porque está en true. 

````
    - name: Apply SELinux role
      block:
        - include_role:
            name: rhel-system-roles.selinux
      rescue:
        - name: Check for failure for other reasons than required reboot
          fail:
          when: not selinux_reboot_required

        - name: Restart managed host
          reboot:

        - name: Reapply SELinux role to complete changes
          include_role:
            name: rhel-system-roles.selinux
````

**VARIABLES DEL ROL**

  VARIABLE selinux_state    ESTABLECE EL MODO EN EL QUE SE EJECUTA SELinux. (enforcing, permissive, disabled). 
                            SI NO ESTA CONFIGURADA EL MODO NO SE CAMBIA.
                            EJ. --> selinux_state: enforcing

VARIABLE selinux_booleans 	TOMA UNA LISTA DE VALORES BOOLEANOS SELinux ORGANIZADOS EN DICCIONARIOS. 

    selinux_booleans:
      - name: 'httpd_enable_homedirs'
        state: 'on'
        persistent: 'yes'

VARIABLE selinux_fcontext REQUIERE UNA LISTA DE CONTEXTOS DE ARCHIVO (semanage fcontext)

    selinux_fcontexts:
      - target: '/srv/www(/.*)?'
        setype: 'httpd_sys_content_t'
        state: 'present'
	
VARIABLE selinux_restore_dirs ESPECIFICA UNA LISTA DE DIRECTORIOS EN LOS QUE EJECUTAR restorecon:

    selinux_restore_dirs:
      - /srv/www

VARIABLE selinux_ports TOMA UNA LISTA DE PUERTOS Y LOS ASIGNA UN CONTEXTO ESPECIFICO DE SELinux.

    selinux_ports:
      - ports: '82'
        setype: 'http_port_t'
        proto: 'tcp'
        state: 'present'

CREACION DE ROLES
-----------------

**PASOS:**
1. Cree la estructura del directorio de roles.
2. Defina el contenido de los roles.
3. Use el rol en una guía.

**DIRECTORIOS DONDE BUSCA ROLES A EJECUTAR**

Busca un directorio roles dentro del directorio de ejecucion de la GUIA. Si no lo encuentra --> roles_path

**EJEMPLO ESTRUCTURA ROL motd**
````
roles/
  └── motd
       ├── defaults
       │     └── main.yml
       ├── files
       ├── handlers
       ├── meta
       │     └── main.yml     <-- autor, licencia, compatibilidad y dependencias del módulo
       ├── README.md          <-- INFORMACION SOBRE EL ROL
       ├── tasks
       │     └── main.yml     <-- PRINCIPAL LISTA DE TAREAS DEL ROL
       └── templates
             └── motd.j2
````

**EJEMPLO DE CREACION DEL ESQUELETO DEL ROLE**

Posicionandote en el directorio donde deseas crear el esqueleto del rol ejecutas:

    ansible-galaxy init <nombre_rol>

**FICHERO roles/motd/tasks/main.yml**

````
---
# tasks file for motd
- name: deliver motd file
  template:
    src: motd.j2
    dest: /etc/motd
    owner: root
    group: root
    mode: 0444
````

**FICHERO roles/motd/templates/motd.j2**

    This is the system {{ ansible_facts['hostname'] }}.
    Today's date is: {{ ansible_facts['date_time']['date'] }}.
    Only use this system with permission.
    You can ask {{ system_owner }} for access.

**FICHERO roles/motd/defaults/main.yml**
````
---
system_owner: user@host.example.com
````

**FICHERO roles/motd/meta/main.yml**
````
---
dependencies:
  - role: apache
    port: 8080
  - role: postgres
    dbname: serverlist
    admin_user: felix
````    
>NOTA: De manera predeterminada, los roles se agregan solamente como una dependencia a una guía solo una vez. 
Si otro rol también lo enumera como una dependencia, no se lo ejecutará nuevamente. Este comportamiento 
se puede reemplazar estableciendo la variable allow_duplicates como yes en el archivo meta/main.yml.

**EJEMPLO GUIA QUE LLAMA AL ROL motd**
````
---
- name: use motd role playbook
  hosts: remote.example.com
  remote_user: devops
  become: true
  roles:
    - motd
````

>NOTA: PODEMOS UTILIZAR LAS VARIABLES DEFINIDAS EN defaults/main.yml. SI UTILIZAMOS EL BLOQUE 'vars:'
AL NIVEL DE 'hosts:', LAS VARIABLES DECLARADAS EN vars (directorio) NO SON SUSTITUIDAS. SI SE DECLARA
DENTRO DE 'roles:' SOBREESCRIBE TANTO LAS DEFINIDAS EN defaults COMO EN vars. 

IMPLEMENTACION DE ROLES CON ANSIBLE GALAXY
------------------------------------------

    https://galaxy.ansible.com
    COMANDO ansible-galaxy

**BUSQUEDA EN ANSIBLE GALAXY**

    ansible-galaxy search 'redis' --platforms EL --author geerlingguy --galaxy-tags <TAG_ROL>

**INFORMACION SOBRE UN ROL DE ANSIBLE GALAXY**

    ansible-galaxy info geerlingguy.redis

**INSTALAR UN ROL DE ANSIBLE GALAXY**

    ansible-galaxy install geerlingguy.redis -p roles/		<-- -p <DIRECTORIO> (roles_path)

**INSTALACION ROLES CON ARCHIVO REQUISITOS**

**FICHERO REQUISITOS EJEMPLO (version es opcional, pero es adecuado ponerlo, o usa la ultima)**

    # from Ansible Galaxy, using the latest version
    - src: geerlingguy.redis

    # from Ansible Galaxy, overriding the name and using a specific version
    - src: geerlingguy.redis
      version: "1.5.0"
      name: redis_prod

    # from any Git-based repository, using HTTPS
    - src: https://gitlab.com/guardianproject-ops/ansible-nginx-acme.git
      scm: git
      version: 56e00a54
      name: nginx-acme

    # from any Git-based repository, using SSH
    - src: git@gitlab.com:guardianproject-ops/ansible-nginx-acme.git
      scm: git
      version: master
      name: nginx-acme-ssh

    # from a role tar ball, given a URL;
    #   supports 'http', 'https', or 'file' protocols
    - src: file:///opt/local/roles/myrole.tar
      name: myrole
  
**INSTALAR DESDE UN FICHERO DE REQUISITOS**

    ansible-galaxy install -r roles/requirements.yml -p roles

**LISTAR ROLES INSTALADOS**

    ansible-galaxy list

**BORRAR UN ROL LOCAL**

    ansible-galaxy remove nginx-acme-ssh

