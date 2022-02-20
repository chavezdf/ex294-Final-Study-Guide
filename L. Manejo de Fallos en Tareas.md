**EVITAR FALLOS EN UNA TAREA (NO PARA LA EJECUCION DE LA GUIA EN EL HOST DEL FALLO)**

    ignore_errors: yes

**FORZADO EJECUCION DE MANEJADORES/HANDLERS (DECLARADO EN playbook, NO EN tarea)**

    force_handlers: yes

**INDICAR QUE LA TAREA FALLA SI NO SE CUMPLE ALGUNA CONDICIONAL (**failed_when**)**

````
tasks:
  - name: Run user creation script
    shell: /usr/local/bin/create_users.sh
    register: command_result
    failed_when: "'Password missing' in command_result.stdout"
````

**USO DEL MODULO FAIL PARA INDICAR QUE UNA TAREA HA FALLADO**

````
tasks:
  - name: Run user creation script
    shell: /usr/local/bin/create_users.sh
    register: command_result
	ignore_errors: yes
	
  - name: Report script failure
	fail:
	  msg: "The password is missing in the output"
	when: "'Password missing' in command_result.stdout"
````

**ESPECIFICAR QUE UNA TAREA INFORME RESULTADOS "changed" **
**TAREA QUE SOLO INFORMA "ok" O "failed" (NO LLAMA A LOS HANDLERS SI NO HAY "changed")**

````
- name: get Kerberos credentials as "admin"
  shell: echo "{{ krb_admin_pass }}" | kinit -f admin
  changed_when: false

**TAREA INFORMA "changed" CUANDO ENCUENTRA 'Success' IN STDOUT**

````
tasks:
  - shell:
      cmd: /usr/local/bin/upgrade-database
    register: command_result
    changed_when: "'Success' in command_result.stdout"
    notify:
      - restart_database
handlers:
  - name: restart_database
    service:
      name: mariadb
      state: restarted
````  

**BLOQUES PARA AGRUPAR TAREAS**

````
- name: block example
  hosts: all
  tasks:
    - name: installing and configuring Yum versionlock plugin
      block:
        - name: package needed by yum
          yum:
            name: yum-plugin-versionlock
            state: present
        - name: lock version of tzdata
          lineinfile:
            dest: /etc/yum/pluginconf.d/versionlock.list
            line: tzdata-2016j-1
            state: present
      when: ansible_distribution == "RedHat"
````

**TAREAS ORGANIZADA EN BLOCK/RESCUE/ALWAYS**

````
  tasks:
    - name: Upgrade DB
      block:
        - name: upgrade the database
		  shell:
			cmd: /usr/local/lib/upgrade-database
	  rescue:
		- name: revert the database upgrade
		  shell:
			cmd: /usr/local/lib/revert-database
	  always:
		- name: always restart the database
		  service:
			name: mariadb
			state: restarted
````

* block:  define las tareas principales para ejecutar.
* rescue: define las tareas que se ejecutarán si fallan las tareas definidas en la cláusula block.
* always: define las tareas que se ejecutarán siempre, independientemente del éxito o de la falla de las tareas definidas en 
			las cláusulas block y rescue.

