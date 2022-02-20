Modulos para modificacion y copia de archivos a hosts administrador
-------------------------------------------------------------------

MODULOS COPIA --> blockinfile, copy, fetch, file, lineinfile, stat, synchronize
MODULO SELINUX --> sefcontext

**files:**
Establece atributos tales como permisos, propiedad, contextos SELinux y marcas de tiempo de archivos regulares, 
enlaces simbólicos, enlaces físicos y directorios. Crear o eliminar archivos regulares, enlaces simbólicos,
enlaces físicos y directorios. 

**copy**:
Copie un archivo de la máquina local o remota a una ubicación en un host administrado. 
Similar al módulo file, el módulo copy también puede establecer atributos de archivo, incluido el contexto SELinux.
OPCION --> force: yes (sobreescribe los ficheros si ya existen y tienen distinto contenido)

**fetch**:
Este módulo funciona como el módulo copy, pero a la inversa. Este módulo se utiliza para recuperar archivos de 
máquinas remotas al nodo de control y para almacenarlos en un árbol de archivos, organizado por nombre de host.

**lineinfile**:
Asegúrese de que una línea particular esté en un archivo o reemplace una línea existente usando una expresión 
regular de referencia inversa. Este módulo es principalmente útil cuando desea cambiar una sola línea en un archivo.
- name: Add a line of text to a file

**blockinfile**:
Inserta, actualiza o elimina un bloque de texto multilínea rodeado por líneas de marcador personalizables.

**stat**:
Recupere información de estado de un archivo, similar al comando stat de Linux.

**synchronize**:
Un contenedor alrededor del comando rsync para hacer que las tareas comunes sean rápidas y fáciles. 
El módulo synchronize no está destinado a proporcionar acceso a toda la potencia del comando rsync, 
pero hace que las invocaciones más comunes sean más fáciles de implementar.
Es posible que aún necesite utilizar el comando rsync con el modulo command según su caso de uso.

**sefcontext (similar a semanage fcontext -a con restorecon)**:
Cambio permantente del contexto de una ruta o fichero
