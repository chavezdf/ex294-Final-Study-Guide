Gestion de Secretos
-------------------
> Para definir el editor a utilizar con ansible-vault, definimos la variable de entorno **EDITOR**

**Creacion de un archivo Cifrado usando password interactivo:**
CREACION DE UN ARCHIVO CIFRADO PASSWORD INTERACTIVO 

    ansible-vault create <filename.yml>

**Creacion de un archivo Cifrado usando password en fichero:**

    ansible-vault create --vault-password-file=<vault-pass> <filename.yml>

**Ver el contenido del fichero Cifrado**

    ansible-vault view <filename.yml>

**Editar fichero Cifrado**

    ansible-vault edit <filename.yml>

**Cifrar fichero(s) existente**

    ansible-vault encrypt <secret1.yml> <secret2.yml>

>NOTA: opcion --output=<OUTPUT_FILE>      <-- solamente se puede usar con un único fichero

**Descifrado de un fichero cifradode forma permanente**

    ansible-vault decrypt <filename>

    ansible-vault decrypt secret1.yml --output=secret1-decrypted.yml

**Cambio de Contraseña de un fichero cifrado de manera interactiva (puede actuar sobre varios ficheros a la vez)0**

    ansible-vault rekey filename 

**Cambio de Contraseña de un fichero cifrado con password en un fichero.**

    ansible-vault rekey --new-vault-password-file=<NEW_VAULT_PASSWORD_FILE> secret.yml

**Solicitar la contraseña de modo interactivo junto a ansible-playbook**

    ansible-playbook --vault-id @prompt <fichero.yml>

>NOTA: En versiones anteriores --ask-vault-pass (pero todos los ficheros deben estar cifrados con el mismo password)

**Suministrar contraseña desde un fichero para usar al lanzar una guia**

    ansible-playbook --vault-password-file=<vault-pw-file> secret.yml

>NOTA: Tambien se puede usar la variable de entorno ANSIBLE_VAULT_PASSWORD_FILE

**Usar varias contraseñas para usar en la ejecucion de guias**

    ansible-playbook --vault-id <etiqueta1>@prompt --vault-id <etiqueta2>@prompt site.yml
    
>NOTA: Si se usó --vault-id <id> al cifrar el fichero, intenta usar la etiqueta que coincide con el <id>.
  

**Donde guardar los ficheros VAULT**

    ├── ansible.cfg
    ├── group_vars
    │ 		└── webservers
    │ 				└── vars
    ├── host_vars
    │ 		└── demo.example.com
    │ 				├── vars
    │ 				└── vault					<-- FICHERO QUE CONTIENE LAS VARIABLES CIFRADAS
    ├── inventory
    └── playbook.yml
