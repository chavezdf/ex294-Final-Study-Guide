Modulos para modificacion y copia de archivos a hosts administrador
-------------------------------------------------------------------

MODULOS COPIA --> blockinfile, copy, fetch, file, lineinfile, stat, synchronize


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

**MODULO SELINUX --> sefcontext**

**sefcontext (similar a semanage fcontext -a con restorecon)**:
Cambio permantente del contexto de una ruta o fichero

**MODULO de SOFTWARE**

* **package**: Manage packages using autodetected package manager native to the operating system
* **yum**: Manage packages using the YUM package manager
* **apt**: Manage packages using the APT package manager
* **dnf**: Manage packages using the DNF package manager
* **gem**: Manage Ruby gems
* **pip**: Manage Python packages from PyPI

**MODULOS DE SISTEMA:**

* **firewalld**: Manage arbitrary ports and services using firewalld
* **reboot**: Reboot a machine
* **service**: Manage services
* **user**: Add, remove, and manage user accounts

**MODULOS DE RED**

* **get_url**: Download files over HTTP, HTTPS, or FTP
* **nmcli**: Manage networking
* **uri**: Interact with web services

