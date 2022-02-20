Modo de Ejecucion:

    TAREA(tasks) --NOTIFICA--> TAREA(handlers)

Los manejadores son tareas que responden a una notificación desencadenada por otras tareas.
Las tareas solo notifican a sus manejadores cuando la tarea cambia algo en un host administrado.
Cada controlador tiene un nombre único a nivel global, y se desencadena al final de un bloque de tareas en una guía. 
Si ninguna tarea notifica al manejador por nombre, no se ejecutará el manejador. 
Si una o más tareas notifican al manejador, el manejador se ejecutará exactamente una vez después de que 
todas las demás tareas en la reproducción se hayan completado. 
Generalmente, los manejadores se utilizan para reiniciar los hosts y los servicios.

NOTA: MISMO NIVEL QUE TASKS
````
tasks:
  - name: copy demo.example.conf configuration template
    template:
	  src: /var/lib/templates/demo.example.conf.template
	  dest: /etc/httpd/conf.d/demo.example.conf
	notify:									<-- notify puede llamar a una lista
	  - restart apache
	  
handlers:
  - name: restart apache					<-- los handlers se ejecutan una sola vez y en el orden
    service:									que se indica en esta seccion (no en el notify), 
      name: httpd								despues de haber finalizado la seccion tasks.
      state: restarted
````

>NOTA: Si una tarea que incluye una declaración notify no informa un resultado changed (por
ejemplo, un paquete ya se encuentra instalado y la tarea muestra ok), no se notificará al
manejador. Se omitirá el manejador, a menos que otra tarea lo notifique. Ansible notifica a los
manejadores solo si la tarea muestra el estado CHANGED.
