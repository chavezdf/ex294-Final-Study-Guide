Ejecucion de Comandos Ad Hoc
----------------------------

Patron a seguir:

    ansible <host-pattern> -m <module> [-a '<module arguments>'] [-i <inventory>]

**Nota:**
* Se utiliza la opción -a para añadir el comando como argumento.
* Se utiliza la opción -o con command para que la respuesta del comando sea en una sola linea.

*Opciones usuales al lanzar comandos AD-HOC*

    FICHERO				OPCION
    inventory 			-i
    remote_user 			-u
    become 				--become, -b
    become_method 			--become-method
    become_user 			--become-user
    become_ask_pass 		--ask-become-pass, -K
    NOTA: no olvidar                --help
    
Modulos a memorizar por su utilidad y frecuecia de uso:
-------------------------------------------------------

    MODULOS ARCHIVOS --> 	copy, file, lineinfile, synchronize, archive, fetch
    MODULOS SOFTWARE --> 	package, yum, apt, dnf, gem, pip
    MODULOS SISTEMA --> 	firewalld, reboot, service, user
    MODULOS NETWORK --> 	get_url, nmcli, uri

*Modulo importante para ejecutar ejecutar comandos de comprobacion, luego de aplicar las guias.

    MODULOS EJECUCION COMANDOS --> 	command, shell, raw  

EJ. ansible <my_managed_hosts> -m command -a /usr/bin/hostname -o


