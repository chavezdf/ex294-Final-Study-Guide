Manejo del Inventario Ansible
------------------

*Grupos por defecto:*

    all y ungrouped

*Rangos:*

    [START:END] [a:z] [1:9] [01:20] 

*Grupo de Grupos en Inventario:*

    [<nombre_grupo>:children]
    grupo1
    grupo2

*Como listar HOSTS:*

    ansible <host/group> --list-hosts

>**_NOTA:_** Si coinciden un grupo y un host, se muestra mensaje indicandolo y se actua sobre el host.

*Archivo de Inventario por defecto:*

    /etc/ansible/hosts

*Como usar el inventario con el comando ansible ó ansible-playbook:*

    -i <PATHNAME>
    --inventory <PATHNAME>
    



    


