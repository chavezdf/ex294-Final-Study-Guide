Gestion de Hechos:
-----------------

Ansible tiene la habilidad de recolectar diferentes datos de los equipos gestionados

**Como obtener los datos usando el modulo Debug en playbook**
````
- name: Fact dump
  hosts: all
  tasks:
    - name: Print all facts
      debug:
        var: ansible_facts
````

**Variables a memorizar, muy utilizadas:**

    DATO                                                 VARIABLE
    Nombre abreviado del host                            ansible_facts['hostname']
    Nombre de dominio completo                           ansible_facts['fqdn']
    Dirección IPv4 principal(basada en enrutamiento      ansible_facts['default_ipv4']['address']
    Lista de los nombres de todas las interfaces de red  ansible_facts['interfaces']
    Tamaño de la partición de disco /dev/vda1            ansible_facts['devices']['vda']['partitions']['vda1']['size']
    Lista de servidores DNS                              ansible_facts['dns']['nameservers']
    Versión del kernel actualmente en ejecución          ansible_facts['kernel']

**Imprimir mensajes de facts con el modulo debug**

````
---
- hosts: all
  tasks:
  - name: Prints various Ansible facts
    debug:
    msg: >
      The default IPv4 address of {{ ansible_facts.fqdn }}
      is {{ ansible_facts.default_ipv4.address }}
````

**Usar comando adhoc usando el modulo setup para ver todos los hechos de un equipo**

    ansible demo1.example.com -m setup

**Desactivar recopilacion de datos**

  gather_facts: no
  
>NOTA: Para activar la recoleccion de datos en una tarea con "father_facts: no" usar el modulo setup.

**Como crear datos personalizados en los hosts administrados**

Se debe crear en la ruta /etc/ansible/facts.d un archivo archivo <fichero.fact> en formato INI o JSON
>OJO: .fact ES NECESARIO!!!

    Formato INI:
    [packages]
    web_package = httpd
    db_package = mariadb-server
    [users]
    user1 = joe
    user2 = jane

    Formato JSON:
    {
      "packages": {
        "web_package": "httpd",
        "db_package": "mariadb-server"
      },
      "users": {
        "user1": "joe",
        "user2": "jane"
      }
    }

Para luego accederlos de la siguiente manera:

    ansible_local.NomFichero.Seccion.NomVariable
    ansible_local[NomFichero][Seccion][NomVariable]

**Ejemplo del acceso a los datos personalizados (ansible_local --> custom --> DATOS, comprobar con el modulo setup)**

    ---
    - hosts: all
      tasks:
      - name: Prints various Ansible facts
        debug:
          msg: >
        The package to install on {{ ansible_facts['fqdn'] }}
        is {{ ansible_facts['ansible_local']['custom']['packages']['web_package'] }}
		
**CONSULTAR VARIABLES MAGICAS**
-------------------------------

    ansible localhost -m debug -a 'var=hostvars["localhost"]'

**Tipos de Variables Magicas**

    hostvars            --> Contiene las variables para hosts administrados y puede usarse para obtener los valores para las 
                            variables de otro host administrado. No incluye los datos del host administrado si no se 
                            recopilaron aún para ese host.
    group_names         --> Enumera todos los grupos en los que se encuentra el host administrado actual.
    groups              -->	Enumera todos los grupos y hosts en el inventario.
    inventory_hostname  --> Contiene el nombre de host para el host administrado actual, según se configuró en el
                            inventario. Este puede ser diferente del nombre de host informado por los datos por diversos motivos.
