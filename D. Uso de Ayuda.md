Access y uso de la Ayuda
------------------------

*Para listar los modulos: (opcion -l)*

    ansible-doc -l

*Puedes intentar ubicar el modulo usando este filtro.*

    ansible-doc -l |grep -i <modulo>

    
*Consultar documentacion de un modulo:*

    ansible-doc <modulo>
    
*Mostrar salida de ejemplo Tipo Guia de un modulo: (opcion -s)	MUY UTIL*

    ansible-doc -s <modulo>

Consultar configuracion de Ansible
----------------------------------

Consultar valores por defecto:

    ansible-config dump |grep -i <parametro>
    
Consultar ayuda de parametros:

    ansible-config list |grep -i <parametro>   
