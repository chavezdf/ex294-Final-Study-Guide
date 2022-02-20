Bucles:
-------

**A) EJEMPLO BUCLE ITERATIVO SOBRE LISTA (item es la variable, loop la palabra clave para bucle)**
````
    - name: Postfix and Dovecot are running
      service:
        name: "{{ item }}"
        state: started
      loop:
        - postfix
        - dovecot
````
**B) EJEMPLO BUCLE ITERATIVO SOBRE VARIABLE TIPO LISTA (recorre cada objeto de la lista)**
````
      vars:
        mail_services:
          - postfix
          - dovecot
      tasks:
        - name: Postfix and Dovecot are running
          service:
            name: "{{ item }}"
            state: started
          loop: "{{ mail_services }}"
````  
**C) EJEMPLO BUCLE UTILIZANDO DICCIONARIOS**
````
      - name: Users exist and are in the correct groups
        user:
          name: "{{ item.name }}"
          state: present
          groups: "{{ item.groups }}"
        loop:
          - name: jane
            groups: wheel
          - name: joe
            groups: root
````
**D) VARIABLES DE REGISTRO EN BUCLES (register: <nombre_variable>)**
````
    ---
    - name: Loop Register Test
      gather_facts: no
      hosts: localhost
      tasks:
        - name: Looping Echo Task
          shell: "echo This is my item: {{ item }}"
          loop:
            - one
            - two
          register: echo_results
        - name: Show echo_results variable
          debug:
            var: echo_results
````            
>NOTA: BUSCAR LA SALIDA DE LA VARIABLE "echo_results"

**E) BUCLES DEPRECATED**

	with_items:
	with_file:
	with_sequence:

Condicionales:
-------------

**EJECUTAR UNA TAREA CONDICIONAL EN FUNCION DE UNA VARIABLE**
````
---
- name: Simple Boolean Task Demo
  hosts: all
  vars:
    run_my_task: true
  tasks:
	- name: httpd package is installed
	  yum:
		name: httpd
	  when: run_my_task
````

**EJECUTAR UNA TAREA CONDICIONAL SI UN PAQUETE ESTA INSTALADO**
````
---
- name: Test Variable is Defined Demo
  hosts: all
  vars:
    my_service: httpd
  tasks:
    - name: "{{ my_service }} package is installed"
      yum:
        name: "{{ my_service }}"
      when: my_service is defined
````

**EJEMPLO CONDICIONES**

    OPERACIÓN 							EJEMPLO
    Igual (el valor es una cadena)      --> ansible_machine == "x86_64"
    Igual (el valor es numérico)        --> max_memory == 512
    Menor que                           --> min_memory < 128
    Mayor que                           --> min_memory > 256
    Menor que o igual a                 --> min_memory <= 256
    Mayor que o igual a                 --> min_memory >= 512
    No es igual a                       --> min_memory != 512
    La variable existe                  --> min_memory is defined
    La variable no existe               --> min_memory is not defined
    La variable booleana es true/1/yes  --> memory_available
    La variable booleana es	false/0/no  --> not memory_available
    El valor de la primera variable    |
    está presente como valor en la     |--> ansible_distribution in supported_distros
    lista de la segunda variable       |
    
>NOTA: Esta ultima variable comprueba que la distribucion se encuentra incluida en la lista.

**CONDICIONES MULTIPLES (and/or)**

    OR         --> when: ansible_distribution == "RedHat" or ansible_distribution == "Fedora"
    AND        --> when: ansible_distribution_version == "7.5" and ansible_kernel == "3.10.0-327.el7.x86_64"
    AND(alt)   --> when:
                     - ansible_distribution_version == "7.5"
                     - ansible_kernel == "3.10.0-327.el7.x86_64"
    AND/OR     --> when: >
                     ( ansible_distribution == "RedHat" and
                     ansible_distribution_major_version == "7" )
                     or
                     ( ansible_distribution == "Fedora" and
                     ansible_distribution_major_version == "28" )
  
COMBINACION BUCLES Y CONDICIONALES
----------------------------------

**EJ 1.**
````
- name: install mariadb-server if enough space on root
  yum:
    name: mariadb-server
    state: latest
  loop: "{{ ansible_mounts }}"			<-- DICCIONARIO SOBRE MONTAJES SISTEMA
  when: item.mount == "/" and item.size_available > 300000000
````  
**EJ 2.**
````
---
- name: Restart HTTPD if Postfix is Running
  hosts: all
  tasks:
    - name: Get Postfix server status
      command: /usr/bin/systemctl is-active postfix
      ignore_errors: yes			<-- no para el procesamiento si el comando falla
      register: result
	  
    - name: Restart Apache HTTPD based on Postfix status
      service:
        name: httpd
        state: restarted
      when: result.rc == 0			<-- salida systemctl es 0 ==> postfix is active
  ````
  
