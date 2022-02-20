Una plantilla Jinja2 está compuesta por múltiples elementos: datos, variables y expresiones. 
Las variables utilizadas en la plantilla se pueden especificar en la sección vars de la guía.
Es posible usar la información de los hosts administrados como variables en una plantilla.

**DELIMITADORES**
````
    {% EXPR %}          para expresiones o lógica (por ejemplo, bucles)
    {{ EXPR }}          se usan para proporcionar los resultados de una expresión o una variable al usuario final
    {​# COMMENT #​}     comentarios
````
>Nota:Generamos ficheros con extension **.j2** que son templates.

**DESPLIEGUE DE UNA TEMPLATE JINJA2 (MODULO template)**
````
tasks:
  - name: template render
    template:
      src: /tmp/j2-template.j2
      dest: /tmp/dest-config-file.txt
````      

**BUCLES FOR**
>NOTA: **loop.index** HACE REFERENCIA AL INDICE UTILIZADO POR EL BUCLE for

**EJ 1. RECORRIDO SOBRE VARIABLE LISTA users**
````
{% for user in users %}
      {{ user }}
{% endfor %}
````


**EJ.2 COMPROBAR CONDICION AL HACER EL RECORRIDO (not root)**
````
{​# for statement #​}
{% for myuser in users if not myuser == "root" %}
User number {{ loop.index }} - {{ myuser }}
{% endfor %}
````

**EJ.3**
````
{% for myhost in groups['myhosts'] %}
{{ myhost }}
{% endfor %}
````

**EJ.4**
````
- name: /etc/hosts is up to date
  hosts: all
  gather_facts: yes
  tasks:
    - name: Deploy /etc/hosts
      template:
        src: templates/hosts.j2
        dest: /etc/hosts
````

**EJ.4 plantilla templates/hosts.j2**
````
{% for host in groups['all'] %}
{{ hostvars[host]['ansible_facts']['default_ipv4']['address'] }} {{ hostvars['host']['ansible_facts']['fqdn'] }} 
	{{ hostvars['host']['ansible_facts']['hostname'] }}
{% endfor %}
````

**USO DE CONDICIONALES**

**IMPRIMIR EL VALOR DE UNA VARIABLE SI SE CUMPLE UNA CONDICION**

````
	{% if <VAR_BOOLEAN> %}
	{{ <VARIABLE> }}
	{% endif %}
````

