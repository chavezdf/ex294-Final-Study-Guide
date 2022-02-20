**GUIAS GRANDES, TENEMOS DOS OPCIONES:**
INCLUIR (include)		<-- DINAMICO, SE AÑADE SEGUN SE VA EJECUTANDO LA GUIA
IMPORTAR (import)		<-- ESTATICO, SE CREA LA GUIA COMPLETA ANTES DE SU EJECUCION

**IMPORTAR GUIAS**
>NOTA: SE PUEDEN INTERCALAR CON TAREAS IMPLICITAS DE LA GUIA
````
    - name: Prepare the web server
      import_playbook: web.yml

    - name: Prepare the database server
      import_playbook: db.yml
````  
**IMPORTAR TAREAS**
````
- name: Install web server
  hosts: webservers
  tasks:
  - import_tasks: webserver_tasks.yml
````

**CASOS DE IMPORTACION TAREAS**

1. import_tasks --> declaraciones condicionales establecidas en la importación, como when, se aplican a cada una de las tareas que se importan.
2. No se puede usar bucles con la función import_tasks.
3. No se puede usar una variable de host o grupo para especificar el nombre del fichero a importar.
	
**INCLUIR TASKS/GUIAS**

**EJ.**
````
  - name: Install web server
    hosts: webservers
    tasks:
    - include_tasks: webserver_tasks.yml
````

**CASOS DE INCLUSION DE TAREAS**

1. Al usar la función include_tasks, las declaraciones condicionales, como when, establecidas determinan si las tareas están 
	incluidas en la reproducción.
2. Si ejecuta ansible-playbook --list-tasks para enumerar las tareas de la guía, no se muestran las tareas en los archivos de 
	tareas incluidos. Se muestran las tareas que incluyen los archivos de tareas. En comparación, la función import_tasks 
	no enumera las tareas que importan archivos de tareas, sino las tareas individuales de los archivos de tareas importados.
3. No puede usar ansible-playbook --start-at-task para iniciar la ejecución de la guía desde una tarea que se encuentra en 
	un archivo de tareas incluido.
4. No puede usar una declaración notify para desencadenar un nombre de controlador que se encuentra en un archivo 
	de tarea incluido. Puede desencadenar un controlador en la guía principal que incluye un archivo de tareas completo, 
	en cuyo caso se ejecutarán todas las tareas en el archivo incluido.
	
**DEFINICION VARIABLES PARA REPRODUCCIONES Y TAREAS EXTERNAS**
- IMPLEMENTAR LAS TAREAS CON VARIABLES
- INCLUIR LAS VARIABLES EN LA GUIA QUE EJECUTA LA TAREA

**ARCHIVO DE TAREAS 'task.yml'**
````
---
  - name: Install the {{ package }} package
    yum:
      name: "{{ package }}"
      state: latest
  - name: Start the {{ service }} service
    service:
      name: "{{ service }}"
      enabled: true
      state: started
````

**IMPORT FICHERO DE TAREAS 'task.yml'**
````
  tasks:
    - name: Import task file and set variables
      import_tasks: task.yml
      vars:
        package: httpd
        service: service
````
>NOTA: 'include' es la directiva de versiones anteriores para realizar la inclusion de playbooks y tareas (DEPRECATED)
