## Configurando todo nuestro inventario

Si vamos a administrar un conjunto de equipos utilizando ansible, hay una serie de consideraciones necesarias para cumplir dicho objetivo:

1. Debemos de contar con un **usuario en comun** para el despliegue los playbooks en todos los equipos, con privilegios administrativos y que pueda ejecutar comandos sin clave.
2. Debemos poder **hacer ssh con dicho usuario sin password**, para ello debemos **compartir la clave publica del usuario**.
3. Debemos hacer que dicho usuario pueda ejecutar cualquier comando privilegiado sin clave.

Para completar esta configuracion inicial en todos nuestros equipos, podemos realizar un script con varios comandos adhocs como se muestra a continuacion:

Esta tarea evidentemente debe ser realizada como usuario root, ya que no hay una configuracion previa en ningun equipo.

````
#!/usr/bin/bash
ansible all -m user -a "name=usuario password={{ 'password' | password_hash('sha512','salt') }}" -u root -k
ansible all -m authorized_key -a "user=usuario key='{{ lookup('file', '/home/usuario/.ssh/id_rsa.pub') }}'" -u root -k
ansible all -m copy -a "content='usuario ALL=(ALL) NOPASSWD: ALL' dest=/etc/sudoers.d/usuario" -u root -k
````

**En este punto, tenomos un nodo de control con una carpeta configurada para realizar despliegues con un usuario diseñado para ello activo y configurado en todos los equipos.**
