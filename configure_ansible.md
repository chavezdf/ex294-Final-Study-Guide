## Configurando tu espacio de trabajo en Ansible

Luego de designar la carpeta de trabajo, debemos de crear los siguientes archivos y carpetas:

Configuracion Personalizada para todos los playbooks de la carpeta:

    ansible.cfg

Archivo de Inventario:

    inventory

Para el Manejo de Roles:

    role/

Para el Manejos de Variables:

    vars/
    host_vars/
    group_vars/

## Archivo de Configuracion inicial:

Nota: Con la instalacion de ansible se copio un archivo con todas las opciones de configuracion que ofrece ansible. Este puede encontrarse en la siguiente ubicacion:
*/etc/ansible.cfg*. Este puede utilizarse para copiar los parametros que se desean utilizar.
````
Definiciones importante en *ansible.cfg*

[defaults]
roles_path = "debe contener todas las rutas donde esten los roles"
inventory = "ruta al archivo de inventario"
ask_pass = "Solicitar password por defecto?"
fork = "Cantidad de equipos a manejar simultaneamente"
remote_user = "Usuario remoto a utilizar"

[privilege_escalation]
become= "Elevar privilegios por defecto?"
become_method= "metodo para elevacion de privilegios"
become_user= "usuario a utilizar al elevar privilegios"
become_ask_pass= "Solicitar password para elevacion?"

````

**Hasta este punto tenemos ansible correctamente instalado en el nodo de control y con una carpeta configurada para realizar los despliegues**

