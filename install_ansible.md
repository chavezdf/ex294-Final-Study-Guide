La manera correcta de instalar ansible en redhat es utilizando el subscription maanger:

Buscamos los repos habilitados de ansible:

    subscription-manager repos --list |grep ansible-2*

Dependiendo de la version que deseas instalar procedes a habilitarla:

    sudo subscription-manager repos --enable ansible-2.8-for-rhel-8-x86_64-rpms

Verificamos la version que se habilito:

    sudo dnf info ansible

Procedemos a instalarla y verificamos la verions:

    sudo dnf install ansible
    sudo dnf --version

Busqueda e Instalacion de Roles del Sistema:

    sudo dnf search roles
    sudo dnf install rhel-system-roles.noarch
