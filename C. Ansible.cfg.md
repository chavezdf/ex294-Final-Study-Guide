Archivos de Configuracion de Ansible:
------------------------------------

*Orden de prioridad en el procesamiento de archivos ansible.cfg*

    1. Variable de entorno ANSIBLE_CONFIG
    2. ./ansible.cfg
    3. ~/.ansible.cfg
    4. /etc/ansible.cfg

Podemos ver cual es el que esta usando con el siguiente comando:

    ansible <host/group> --list-hosts -v

*Opciones importantes a configurar en el archivo:*

    [defaults]
    inventory=        <--	Indica el fichero de inventario
    remote_user=      <--	Usuario remoto a utilizar con el que se inicia la conexion 
		                  (si no se define, se usa el usuario actual)
    ask_pass=         <--	true/false

    [privilege_escalation]
    become=           <--	true/false Si se debe cambiar automáticamente el usuario en el
		                  host administrado después de la conexión.
    become_method=    <--	Metodo a utilizar (su o sudo, por ejemplo)
    become_user=      <--	Usuario al que escalar privilegios
    become_ask_pass=  <--	true/false (default false)

    MODOS DE CONEXION:
    FICHERO ./host-vars/<nombre-host>
    VARIABLE ansible-connection: local (localhost)
    VARIABLE ansible-connection: smart (resto) 
    VARIABLE ansible-port: <puerto>

    COMENTARIOS EN FICHERO:
    # --> Comenta la línea entera
    ; --> Admite directivas a la izquierda en la misma linea

