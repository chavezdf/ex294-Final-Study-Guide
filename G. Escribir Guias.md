Cabecera mas comun:
-------------------
````
---
- name: Nobmre o Titulo
  hosts: <host/grupo>
  become: <true/false>            <-- Si estas seguro de que necesitas privilegios
  gather_facts: <true/false>      <-- Si necesitas que se recabe la informacion en los equipos
  gather_subset: <hardware,etc>   <-- Se puede solicitar el subconjunto necesario en vez de traerlos todos.
  serial: <#host x Lote>          <-- Cantidad de equipos que ejecutaran el play simultaneamente por baches.
  vars:				  <-- Definision de variable directamente
  vars_files:		          <-- Definicion de variable en ficheros
  tasks:
  - name: Nombre de Tarea
    <modulo>
      <parametro1>: <valor>
      <parametro2>: <valor>
      ...
      <parametroN>: <valor>        
````

Paralelismo:
----------

Ansible maneja de 2 maneras el control de ejecucion de las tareas. Estos valores estan configurados por defecto en ansible.cfg

**Prinicpio basico:**
Las tareas se ejecutan una a una en todos los hosts adminsitrados. Para manejar con eficiencia las ejecuciones, estas se hacen por lotes.
				
**Forks:** Cantidad maxima de conecciones simultaneas (paralelismo) para la ejecucion de una tarea. Por defecto esta configurado **5**.
**Serial:** Ejecucion por lotes (Rolling) de una play completa. Esto se configura para permitir la gestion de Rolling Updates.
        
Gestion de Variables:
------------------------

**Sintaxis:**

- Iniciarse con una letra
- letra + numero + '_'permitida:

**Definicion Directa en la Guia:**

	vars:
		user: jose
		home: /home/jose

**Definicion en ficheros**

	vars_files:
		- vars/usuarios.yml

**Referencia a Valor de Variables**

		{{ user }}
		
>NOTA: Usar "{{ user }}" cuando se quiera utilizar como cadena

**Definir Variables en Inventario (HOST/GRUPO)**

[servers]
demo.example.com ansible_user=joe			<-- Definicion de variable de host

[servers:vars]								<-- Definicion de variable de grupo
user=joe

**Uso de directorios de variables de host u grupo**

1. Crear dos directorios en el directorio de trabajo --> group_vars y host_vars
2. Debajo de cada directorio se genera un fichero con el host/grupo.
3. En cada fichero se definen las variables para el host/grupo.

		ESTRUCTURA DE EJEMPLO
		project
		├── ansible.cfg
		├── group_vars
		│ 		├── datacenters
		│ 		├── datacenters1
		│ 		└── datacenters2
		├── host_vars
		│ 		├── demo1.example.com
		│ 		├── demo2.example.com
		│ 		├── demo3.example.com
		│ 		└── demo4.example.com
		├── inventory
		└── playbook.yml

**Variables de Global Scope**

		ansible-playbook <guia.yml> -e "package=apache"

**Uso de Matrices**

		users:
			bjones:
				first_name: Bob
				last_name: Jones
				home_dir: /users/bjones
			acook:
				first_name: Anne
				last_name: Cook
				home_dir: /users/acook

		**Como devolver 'Bob'**
		
				users['bjones']['first_name']		ó	users.bjones.first_name
				
		**Como devolver '/users/acook'**

				users['acook']['home_dir']		ó		users.acook.home_dir

**Uso de Variables Registradas**

Se puede declarar una variable en una tarea y hacer uso despues del modulo debug:

		---
		- name: Installs a package and prints the result
			hosts: all
			tasks:
			- name: Install the package
				yum:
					name: httpd
					state: installed
				register: install_result

			- debug: var=install_result
